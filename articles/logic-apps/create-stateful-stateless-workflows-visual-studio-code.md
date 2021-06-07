---
title: Criar fluxos de trabalho de visualização de aplicativos lógicos no Visual Studio Code
description: Crie e execute fluxos de trabalho para cenários de automação e integração no Visual Studio Code com a extensão de aplicativos lógicos do Azure (versão prévia).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: f7f8082cc9120345336610d5cb49741140d3b606
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557005"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Criar fluxos de trabalho com e sem estado no Visual Studio Code com a extensão de aplicativos lógicos do Azure (versão prévia)

> [!IMPORTANT]
> Essa versão preliminar está na versão prévia pública, e é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho em produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com a [visualização dos aplicativos lógicos do Azure](logic-apps-overview-preview.md), você pode criar soluções de automação e integração entre aplicativos, dados, serviços de nuvem e sistemas criando e executando aplicativos lógicos que incluem [fluxos de trabalho com e *sem*](logic-apps-overview-preview.md#stateful-stateless) estado no Visual Studio Code usando a extensão de aplicativos lógicos do Azure (versão prévia). Usando esse novo tipo de aplicativo lógico, você pode criar vários fluxos de trabalho que são alimentados pelo tempo de execução da visualização dos aplicativos lógicos do Azure reprojetados, que fornece portabilidade, melhor desempenho e flexibilidade para implantação e execução em vários ambientes de hospedagem, não apenas no Azure, mas também em contêineres do Docker. Para saber mais sobre o novo tipo de aplicativo lógico, consulte [visão geral para visualização de aplicativos lógicos do Azure](logic-apps-overview-preview.md).

![Captura de tela que mostra Visual Studio Code, projeto de aplicativo lógico e fluxo de trabalho.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

No Visual Studio Code, você pode começar criando um projeto onde você pode criar e executar *localmente* os fluxos de trabalho do aplicativo lógico em seu ambiente de desenvolvimento usando a extensão de aplicativos lógicos do Azure (versão prévia). Embora você também possa começar [criando um novo recurso de **aplicativo lógico (versão prévia)** no portal do Azure](create-stateful-stateless-workflows-azure-portal.md), ambas as abordagens fornecem a capacidade de implantar e executar seu aplicativo lógico nos mesmos tipos de ambientes de hospedagem.

Enquanto isso, você ainda pode criar o tipo de aplicativo lógico original. Embora as experiências de desenvolvimento em Visual Studio Code sejam diferentes entre os tipos de aplicativo lógico original e novo, sua assinatura do Azure pode incluir ambos os tipos. Você pode exibir e acessar todos os aplicativos lógicos implantados em sua assinatura do Azure, mas os aplicativos são organizados em suas próprias categorias e seções.

Este artigo mostra como criar seu aplicativo lógico e um fluxo de trabalho no Visual Studio Code usando a extensão de aplicativos lógicos do Azure (versão prévia) e executando estas tarefas de alto nível:

* Crie um projeto para seu aplicativo lógico e fluxo de trabalho.

* Adicionar um gatilho e uma ação.

* Execute, teste, depure e examine o histórico de execução localmente.

* Localize os detalhes do nome de domínio para acesso ao firewall.

* Implante no Azure, que inclui, opcionalmente, a Application Insights.

* Gerencie seu aplicativo lógico implantado no Visual Studio Code e no portal do Azure.

* Habilite o histórico de execuções para fluxos de trabalho sem estado.

* Habilite ou abra o Application Insights após a implantação.

* Implante em um contêiner do Docker que você pode executar em qualquer lugar.

> [!NOTE]
> Para obter informações sobre os problemas atuais conhecidos, examine a [página problemas conhecidos da visualização pública de aplicativos lógicos no GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="access-and-connectivity"></a>Acesso e conectividade

* Acesso à Internet para que você possa baixar os requisitos, conectar-se de Visual Studio Code à sua conta do Azure e publicar de Visual Studio Code para o Azure, um contêiner do Docker ou outro ambiente.

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Para criar o mesmo exemplo de aplicativo lógico neste artigo, você precisa de uma conta de email do Office 365 Outlook que usa uma conta corporativa ou de estudante da Microsoft para entrar.

  Se você optar por usar um [conector de email diferente com suporte dos aplicativos lógicos do Azure](/connectors/), como Outlook.com ou [gmail](../connectors/connectors-google-data-security-privacy-policy.md), ainda poderá seguir o exemplo, e as etapas gerais geral são as mesmas, mas a interface do usuário e as opções podem ser diferentes de algumas maneiras. Por exemplo, se você usar o conector Outlook.com, use seu conta Microsoft pessoal em vez de entrar.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Requisitos de armazenamento

#### <a name="windows"></a>Windows

Para criar e executar localmente seu projeto de aplicativo lógico no Visual Studio Code ao usar o Windows, siga estas etapas para configurar o emulador de armazenamento do Azure:

1. Baixe e instale o [emulador de armazenamento do Azure 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Se você ainda não tiver um, precisará ter uma instalação local do SQL DB, como a versão gratuita [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658), para que o emulador possa ser executado.

   Para obter mais informações, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](../storage/common/storage-use-emulator.md).

1. Antes de executar o projeto, certifique-se de iniciar o emulador.

   ![Captura de tela que mostra o emulador de armazenamento do Azure em execução.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS e Linux

Para compilar e executar localmente seu projeto de aplicativo lógico no Visual Studio Code ao usar o macOS ou Linux, siga estas etapas para criar e configurar uma conta de armazenamento do Azure.

> [!NOTE]
> Atualmente, o designer no Visual Studio Code não funciona no sistema operacional Linux, mas você ainda pode executar compilar, executar e implantar aplicativos lógicos que usam o tempo de execução de visualização dos aplicativos lógicos para máquinas virtuais baseadas em Linux. Por enquanto, você pode criar seus aplicativos lógicos no Visual Studio Code no Windows ou no macOS e, em seguida, implantar em uma máquina virtual baseada em Linux.

1. Entre no [portal do Azure](https://portal.azure.com)e [crie uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal), que é um [pré-requisito para Azure Functions](../azure-functions/storage-considerations.md).

1. No menu conta de armazenamento, em **configurações**, selecione **chaves de acesso**.

1. No painel **chaves de acesso** , localize e copie a cadeia de conexão da conta de armazenamento, que é semelhante a este exemplo:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Captura de tela que mostra a portal do Azure com chaves de acesso da conta de armazenamento e cadeia de conexão copiadas.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Para obter mais informações, consulte [gerenciar chaves de conta de armazenamento](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Salve a cadeia de conexão em algum lugar seguro. Depois de criar seu projeto de aplicativo lógico no Visual Studio Code, você precisa adicionar a cadeia de caracteres à **local.settings.jsno** arquivo na pasta de nível raiz do projeto.

   > [!IMPORTANT]
   > Se você planeja implantar em um contêiner do Docker, também precisará adicionar essa cadeia de conexão ao arquivo do Docker que você usa para implantação.

### <a name="tools"></a>Ferramentas

* [Visual Studio Code 1.30.1 (janeiro de 2019) ou superior](https://code.visualstudio.com/), que é gratuito. Além disso, baixe e instale essas ferramentas para Visual Studio Code, se você ainda não as tiver:

  * [Extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), que fornece uma única experiência comum de entrada e filtragem de assinatura do Azure para todas as outras extensões do azure no Visual Studio Code.

  * [C# para Visual Studio Code extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), que habilita a funcionalidade F5 para executar seu aplicativo lógico.

  * [Azure Functions Core Tools 3.0.3245 ou posterior](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) usando a versão do Microsoft Installer (MSI), que é `func-cli-3.0.3245-x*.msi` .

    Essas ferramentas incluem uma versão do mesmo tempo de execução que alimenta o Azure Functions Runtime, que a extensão de visualização usa em Visual Studio Code.

    > [!IMPORTANT]
    > Se você tiver uma instalação anterior a essas versões, desinstale essa versão primeiro ou certifique-se de que a variável de ambiente PATH aponta para a versão que você baixa e instala.

  * [Extensão de aplicativos lógicos do Azure (versão prévia) para Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Essa extensão fornece a capacidade de criar aplicativos lógicos em que você pode criar fluxos de trabalho com e sem estado que são executados localmente no Visual Studio Code e, em seguida, implantar esses aplicativos lógicos diretamente no Azure ou em contêineres do Docker.

    No momento, você pode ter a extensão original de aplicativos lógicos do Azure e a extensão de visualização pública instalada no Visual Studio Code. Embora as experiências de desenvolvimento sejam diferentes de algumas maneiras entre as extensões, sua assinatura do Azure pode incluir os tipos de aplicativos lógicos que você cria com as extensões. Visual Studio Code mostra todos os aplicativos lógicos implantados em sua assinatura do Azure, mas os organiza em diferentes seções por nomes de extensão, **aplicativos lógicos** e **aplicativos lógicos do Azure (versão prévia)**.

    > [!IMPORTANT]
    > Se você criou projetos de aplicativo lógico com a extensão de visualização privada anterior, esses projetos não funcionarão com a extensão de visualização pública. No entanto, você pode migrar esses projetos depois de desinstalar a extensão de visualização privada, excluir os arquivos associados e instalar a extensão de visualização pública. Em seguida, você cria um novo projeto no Visual Studio Code e copia o arquivo **Workflow. Definition** do aplicativo lógico criado anteriormente para o novo projeto. Para obter mais informações, consulte [migrar da extensão de visualização privada](#migrate-private-preview).
    > 
    > Se você criou projetos de aplicativo lógico com a extensão de visualização pública anterior, poderá continuar usando esses projetos sem nenhuma etapa de migração.

    **Para instalar a extensão de **aplicativos lógicos do Azure (versão prévia)** , siga estas etapas:**

    1. No Visual Studio Code, na barra de ferramentas à esquerda, selecione **extensões**.

    1. Na caixa de pesquisa extensões, digite `azure logic apps preview` . Na lista de resultados, selecione **instalação de aplicativos lógicos do Azure (versão prévia)** **>** .

       Após a conclusão da instalação, a extensão de visualização aparecerá na lista **extensões: instaladas** .

       ![Captura de tela que mostra Visual Studio Code lista de extensões instaladas com a extensão "aplicativos lógicos do Azure (visualização)" sublinhada.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Se a extensão não aparecer na lista instalada, tente reiniciar Visual Studio Code.

* Para usar a [ação de operações de código embutido](../logic-apps/logic-apps-add-run-inline-code.md) que executa o JavaScript, instale [Node.js versões 10. x, 11. x. x ou 12. x](https://nodejs.org/en/download/releases/). x.

  > [!TIP] 
  > Para o Windows, baixe a versão do MSI. Se você usar a versão ZIP em vez disso, precisará disponibilizar manualmente Node.js usando uma variável de ambiente PATH para seu sistema operacional.

* Para executar localmente gatilhos e ações baseados em webhook, como o [gatilho interno de webhook http](../connectors/connectors-native-webhook.md), no Visual Studio Code, você precisa [Configurar o encaminhamento para a URL de retorno de chamada](#webhook-setup).

* Para testar o aplicativo lógico de exemplo que você cria neste artigo, você precisa de uma ferramenta que possa enviar chamadas para o gatilho de solicitação, que é a primeira etapa no aplicativo lógico de exemplo. Se você não tiver essa ferramenta, poderá baixar, instalar e usar o [postmaster](https://www.postman.com/downloads/).

* Se você criar seu aplicativo lógico e implantar com configurações que dão suporte ao uso de [Application insights](../azure-monitor/app/app-insights-overview.md), você pode opcionalmente habilitar o log de diagnóstico e o rastreamento para seu aplicativo lógico. Isso pode ser feito quando você implanta seu aplicativo lógico do Visual Studio Code ou após a implantação. Você precisa ter uma instância de Application Insights, mas você pode criar esse recurso com [antecedência](../azure-monitor/app/create-workspace-resource.md), ao implantar seu aplicativo lógico ou após a implantação.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migrar da extensão de visualização privada

Qualquer projeto de aplicativo lógico criado com a extensão **aplicativos lógicos do Azure (versão prévia privada)** não funcionará com a extensão de visualização pública. No entanto, você pode migrar esses projetos para novos projetos seguindo estas etapas:

1. Desinstale a extensão de visualização particular.

1. Exclua qualquer pacote de extensão associado e pastas de pacote NuGet nestes locais:

   * A pasta **Microsoft. Azure. Functions. ExtensionBundle. workflows** , que contém pacotes de extensão anteriores e está localizada ao longo de um dos caminhos aqui:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * A pasta **Microsoft. Azure. workflows. webjobs. Extension** , que é o cache do [NuGet](/nuget/what-is-nuget) para a extensão de visualização particular e está localizada ao longo deste caminho:

     `C:\Users\{userName}\.nuget\packages`

1. Instale a extensão de **aplicativos lógicos do Azure (versão prévia)** .

1. Crie um novo projeto no Visual Studio Code.

1. Copie o arquivo **Workflow. Definition** do aplicativo lógico criado anteriormente para o novo projeto.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurar o Visual Studio Code

1. Para certificar-se de que todas as extensões estejam instaladas corretamente, recarregue ou reinicie Visual Studio Code.

1. Confirme se Visual Studio Code localiza e instala automaticamente atualizações de extensão para que sua extensão de visualização obtenha as atualizações mais recentes. Caso contrário, você precisará desinstalar manualmente a versão desatualizada e instalar a versão mais recente.

   1. No menu **arquivo** , acesse **preferências** **>** **configurações**.

   1. Na guia **usuário** , vá para extensões de **recursos** **>** .

   1. Confirme se a **seleção automática de atualizações** e **atualização automática** está selecionada.

Além disso, por padrão, as seguintes configurações são habilitadas e definidas para a extensão de visualização de aplicativos lógicos:

* **Aplicativos lógicos do Azure v2: tempo de execução do projeto**, que é definido para a versão **~ 3**

  > [!NOTE]
  > Esta versão é necessária para usar as [ações de operações de código embutido](../logic-apps/logic-apps-add-run-inline-code.md).

* **Aplicativos lógicos do Azure v2: Gerenciador de exibição experimental**, que habilita o designer mais recente no Visual Studio Code. Se você tiver problemas no designer, como arrastar e soltar itens, desative essa configuração.

Para localizar e confirmar essas configurações, siga estas etapas:

1. No menu **arquivo** , acesse **preferências** **>** **configurações**.

1. Na guia **usuário** , vá para **>** **extensões** **>** **aplicativos lógicos do Azure (versão prévia)**.

   Por exemplo, você pode encontrar a configuração **aplicativos lógicos do Azure v2: tempo de execução do projeto** aqui ou usar a caixa de pesquisa para localizar outras configurações:

   ![Captura de tela que mostra as configurações de Visual Studio Code para a extensão "aplicativos lógicos do Azure (visualização)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure.

   ![Captura de tela que mostra Visual Studio Code barra de atividades e o ícone do Azure selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. No painel do Azure, em **Azure: aplicativos lógicos (versão prévia)**, selecione **entrar no Azure**. Quando a página de autenticação do Visual Studio Code for exibida, entre com sua conta do Azure.

   ![Captura de tela que mostra o painel do Azure e o link selecionado para entrar no Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Depois de entrar, o painel do Azure mostra as assinaturas em sua conta do Azure. Se você também tiver a extensão lançada publicamente, poderá encontrar todos os aplicativos lógicos que criou com essa extensão na seção **aplicativos lógicos** , não na seção **aplicativos lógicos (versão prévia)** .
   
   Se as assinaturas esperadas não aparecerem ou se você quiser que o painel mostre apenas assinaturas específicas, siga estas etapas:

   1. Na lista assinaturas, mova o ponteiro ao lado da primeira assinatura até que o botão **selecionar assinaturas** (ícone de filtro) seja exibido. Selecione o ícone de filtro.

      ![Captura de tela que mostra o painel do Azure e o ícone de filtro selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Ou, na barra de status Visual Studio Code, selecione sua conta do Azure. 

   1. Quando outra lista de assinaturas for exibida, selecione as assinaturas desejadas e, em seguida, certifique-se de selecionar **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Criar um projeto local

Antes de criar seu aplicativo lógico, crie um projeto local para que você possa gerenciar, executar e implantar seu aplicativo lógico do Visual Studio Code. O projeto subjacente é semelhante a um projeto Azure Functions, também conhecido como um projeto de aplicativo de funções. No entanto, esses tipos de projeto são separados uns dos outros, portanto, os aplicativos lógicos e os aplicativos de função não podem existir no mesmo projeto.

1. Em seu computador, crie uma pasta local *vazia* a ser usada para o projeto que você criará posteriormente no Visual Studio Code.

1. Em Visual Studio Code, feche qualquer e todas as pastas abertas.

1. No painel do Azure, ao lado de **Azure: aplicativos lógicos (versão prévia)**, selecione **criar novo projeto** (ícone que mostra uma pasta e um raio).

   ![Captura de tela que mostra a barra de ferramentas do painel do Azure com "criar novo projeto" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Se o Windows Defender firewall solicitar que você conceda acesso à rede para `Code.exe` , que é Visual Studio Code e, para `func.exe` , que é o Azure Functions Core Tools, selecione **redes privadas, como minha rede doméstica ou de trabalho** **>** **permitir acesso**.

1. Navegue até o local onde você criou a pasta do projeto, selecione essa pasta e continue.

   ![Captura de tela que mostra a caixa de diálogo "Selecionar pasta" com uma pasta de projeto recém-criada e o botão "selecionar" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Na lista de modelos exibida, selecione fluxo de **trabalho com estado** ou **fluxo de trabalho sem estado**. Este exemplo seleciona **fluxo de trabalho com estado**.

   ![Captura de tela que mostra a lista de modelos de fluxo de trabalho com "fluxo de trabalho com estado" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Forneça um nome para seu fluxo de trabalho e pressione Enter. Este exemplo usa `Fabrikam-Stateful-Workflow` como o nome.

   ![Captura de tela que mostra a caixa "criar novo fluxo de trabalho com estado (3/4)" e "fabrikam-estado-fluxo de trabalho" como o nome do fluxo de trabalho.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code conclui a criação do projeto e abre o **workflow.jsno** arquivo para o fluxo de trabalho no editor de códigos.

   > [!NOTE]
   > Se você for solicitado a selecionar como abrir seu projeto, selecione **abrir na janela atual** se desejar abrir o projeto na janela de Visual Studio Code atual. Para abrir uma nova instância para Visual Studio Code, selecione **abrir em nova janela**.

1. Na barra de ferramentas do Visual Studio, abra o painel Gerenciador, se ainda não estiver aberto.

   O painel Gerenciador mostra seu projeto, que agora inclui arquivos de projeto gerados automaticamente. Por exemplo, o projeto tem uma pasta que mostra o nome do fluxo de trabalho. Dentro dessa pasta, o **workflow.jsno** arquivo contém a definição de JSON subjacente do fluxo de trabalho.

   ![Captura de tela que mostra o painel do Explorer com a pasta do projeto, a pasta do fluxo de trabalho e o arquivo "workflow.jsem".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Se você estiver usando o macOS ou Linux, configure o acesso à sua conta de armazenamento seguindo estas etapas, que são necessárias para executar localmente seu projeto:

   1. Na pasta raiz do projeto, abra o **local.settings.jsno** arquivo.

      ![Captura de tela que mostra o painel do Explorer e o arquivo ' local.settings.jsem ' em seu projeto.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Substitua o `AzureWebJobsStorage` valor da propriedade pela cadeia de conexão da conta de armazenamento que você salvou anteriormente, por exemplo:

      Antes:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      Após:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

   1. Quando terminar, certifique-se de salvar suas alterações.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Habilitar a criação de conectores internos

Você pode criar seus próprios conectores internos para qualquer serviço que precisar usando a estrutura de [extensibilidade da versão de visualização](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Semelhante a conectores internos, como o barramento de serviço do Azure e o SQL Server, esses conectores fornecem maior taxa de transferência, baixa latência, conectividade local e execução nativa no mesmo processo que o tempo de execução de visualização.

No momento, a capacidade de criação está disponível apenas no Visual Studio Code, mas não está habilitada por padrão. Para criar esses conectores, primeiro você precisa converter seu projeto do baseado em pacote de extensão (Node.js) para o baseado em pacote NuGet (.NET).

> [!IMPORTANT]
> Essa ação é uma operação unidirecional que não pode ser desfeita.

1. No painel do Explorer, na raiz do seu projeto, mova o ponteiro do mouse sobre qualquer área em branco abaixo de todos os outros arquivos e pastas, abra o menu de atalho e selecione **converter em projeto de aplicativo lógico baseado em NuGet**.

   ![Captura de tela que mostra que mostra o painel do Explorer com o menu de atalho do projeto aberto de uma área em branco na janela do projeto.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Quando o prompt for exibido, confirme a conversão do projeto.

1. Para continuar, examine e siga as etapas no artigo [aplicativos lógicos do Azure que executam extensibilidade de conector interno em qualquer lugar](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Abrir o arquivo de definição de fluxo de trabalho no designer

1. Verifique as versões que estão instaladas no seu computador executando este comando:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Se você tiver SDK do .NET Core 5. x, essa versão poderá impedi-lo de abrir a definição de fluxo de trabalho subjacente do aplicativo lógico no designer. Em vez de desinstalar esta versão, na pasta raiz do seu projeto, crie uma **global.jsno** arquivo que faz referência à versão 3. x do tempo de execução do .NET Core que você tem, por exemplo:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Certifique-se de adicionar explicitamente o **global.jsno** arquivo na pasta raiz do projeto de dentro Visual Studio Code. Caso contrário, o designer não será aberto.

1. Expanda a pasta do projeto do seu fluxo de trabalho. Abra o **workflow.jsno** menu de atalho do arquivo e selecione **abrir no designer**.

   ![Captura de tela que mostra o painel do Explorer e a janela de atalho para a workflow.jsno arquivo com "abrir no designer" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Na lista **habilitar conectores no Azure** , selecione **usar conectores do Azure**, que se aplica a todos os conectores gerenciados que estão disponíveis e implantados no Azure, não apenas aos conectores dos serviços do Azure.

   ![Captura de tela que mostra o painel do Explorer com a lista "Habilitar conectores no Azure" aberta e "usar conectores do Azure" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Atualmente, os fluxos de trabalho sem estado dão suporte apenas a *ações* para [conectores gerenciados](../connectors/apis-list.md#managed-api-connectors), que são implantados no Azure e não gatilhos. Embora você tenha a opção de habilitar conectores no Azure para seu fluxo de trabalho sem estado, o designer não mostra nenhum gatilho de conector gerenciado para você selecionar.

1. Na lista **selecionar assinatura** , selecione a assinatura do Azure a ser usada para seu projeto de aplicativo lógico.

   ![Captura de tela que mostra o painel do Explorer com a caixa "selecionar assinatura" e sua assinatura selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Na lista grupos de recursos, selecione **criar novo grupo de recursos**.

   ![Captura de tela que mostra o painel do Explorer com a lista grupos de recursos e "criar novo grupo de recursos" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Forneça um nome para o grupo de recursos e pressione Enter. Este exemplo usa o `Fabrikam-Workflows-RG`.

   ![Captura de tela que mostra o painel do Explorer e a caixa nome do grupo de recursos.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Na lista locais, localize e selecione a região do Azure a ser usada ao criar o grupo de recursos e os recursos. Este exemplo usa **EUA Central ocidental**.

   ![Captura de tela que mostra o painel do Explorer com a lista de locais e "Oeste EUA Central" selecionados.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Depois de executar essa etapa, Visual Studio Code abrirá o designer de fluxo de trabalho.

   > [!NOTE]
   > Quando Visual Studio Code inicia a API de tempo de design do fluxo de trabalho, você pode receber uma mensagem informando que a inicialização pode levar alguns segundos. Você pode ignorar esta mensagem ou selecionar **OK**.
   >
   > Se o designer não abrir, examine a seção solução de problemas, o [Designer não poderá ser aberto](#designer-fails-to-open).

   Depois que o Designer for exibido, o prompt **escolher um operação** aparecerá no designer e será selecionado por padrão, que mostra o painel **Adicionar uma ação** .

   ![Captura de tela que mostra o designer de fluxo de trabalho.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Em seguida, [adicione um gatilho e ações](#add-trigger-actions) ao seu fluxo de trabalho.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Adicionar um gatilho e ações

Depois de abrir o designer, o prompt **escolher um operação** aparecerá no designer e será selecionado por padrão. Agora você pode começar a criar seu fluxo de trabalho adicionando um gatilho e ações.

O fluxo de trabalho neste exemplo usa esse gatilho e estas ações:

* O [gatilho de solicitação](../connectors/connectors-native-reqres.md)interno, **quando uma solicitação HTTP é recebida**, que recebe chamadas de entrada ou solicitações e cria um ponto de extremidade que outros serviços ou aplicativos lógicos podem chamar.

* A [ação do Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md), **enviar um email**.

* A [ação de resposta](../connectors/connectors-native-reqres.md)interna, que você usa para enviar uma resposta e retornar dados de volta para o chamador.

### <a name="add-the-request-trigger"></a>Adicionar o gatilho de Solicitação

1. Ao lado do designer, no painel **Adicionar um gatilho** , na caixa de pesquisa **escolher uma operação** , certifique-se de que o **interno** está selecionado para que você possa selecionar um gatilho que seja executado nativamente.

1. Na caixa de pesquisa **escolher uma operação** , insira `when a http request` e selecione o gatilho de solicitação interno que é nomeado **quando uma solicitação HTTP é recebida**.

   ![Captura de tela que mostra o designer de fluxo de trabalho e * * adicionar um painel de gatilho * * com o gatilho "quando um pedido HTTP é recebido" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Quando o gatilho aparece no designer, o painel de detalhes do gatilho é aberto para mostrar as propriedades, as configurações e outras ações do gatilho.

   ![Captura de tela que mostra o designer de fluxo de trabalho com o gatilho "quando uma solicitação HTTP é recebida" está selecionado e o painel detalhes do gatilho é aberto.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se o painel de detalhes não for exibido, verifique se o gatilho está selecionado no designer.

1. Se você precisar excluir um item do designer, [siga estas etapas para excluir itens do designer](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Adicionar a ação do Outlook do Office 365

1. No designer, no gatilho que você adicionou, selecione **nova etapa**.

   O prompt **escolher uma operação** aparece no designer e o painel **Adicionar uma ação** é reaberto para que você possa selecionar a próxima ação.

1. No painel **Adicionar uma ação** , na caixa de pesquisa **escolher uma operação** , selecione **Azure** para que você possa encontrar e selecionar uma ação para um conector gerenciado que é implantado no Azure.

   Este exemplo seleciona e usa a ação do Outlook do Office 365, **enviar um email (v2)**.

   ![Captura de tela que mostra o designer de fluxo de trabalho e * * adicionar um painel ação * * com o Office 365 Outlook "enviar um email" ação selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. No painel de detalhes da ação, selecione **entrar** para que você possa criar uma conexão com sua conta de email.

   ![Captura de tela que mostra o designer de fluxo de trabalho e * * enviar um email (v2) * * painel com "entrar" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Quando Visual Studio Code solicitar o consentimento para acessar sua conta de email, selecione **abrir**.

   ![Captura de tela que mostra o Visual Studio Code prompt para permitir o acesso.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Para evitar prompts futuros, selecione **configurar domínios confiáveis** para que você possa adicionar a página de autenticação como um domínio confiável.

1. Siga os prompts subsequentes para entrar, permitir o acesso e permitir o retorno a Visual Studio Code.

   > [!NOTE]
   > Se houver muito tempo antes de concluir os prompts, o processo de autenticação atingirá o tempo limite e falhará. Nesse caso, retorne ao designer e tente entrar novamente para criar a conexão.

1. Quando a extensão aplicativos lógicos do Azure (versão prévia) solicitar o consentimento para acessar sua conta de email, selecione **abrir**. Siga o prompt subsequente para permitir o acesso.

   ![Captura de tela que mostra o prompt de extensão de visualização para permitir o acesso.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Para evitar solicitações futuras, selecione **não perguntar novamente para esta extensão**.

   Depois que Visual Studio Code criar sua conexão, alguns conectores mostrarão a mensagem `The connection will be valid for {n} days only` . Esse limite de tempo aplica-se apenas à duração enquanto você cria seu aplicativo lógico no Visual Studio Code. Após a implantação, esse limite não se aplica mais porque seu aplicativo lógico pode autenticar em tempo de execução usando sua [identidade gerenciada atribuída pelo sistema](../logic-apps/create-managed-service-identity.md)habilitada automaticamente. Essa identidade gerenciada difere das credenciais de autenticação ou da cadeia de conexão que você usa ao criar uma conexão. Se você desabilitar essa identidade gerenciada atribuída pelo sistema, as conexões não funcionarão no tempo de execução.

1. No designer, se a ação **enviar um email** não aparecer selecionada, selecione essa ação.

1. No painel detalhes da ação, na guia **parâmetros** , forneça as informações necessárias para a ação, por exemplo:

   ![Captura de tela que mostra o designer de fluxo de trabalho com detalhes para a ação "enviar um email" do Outlook do Office 365.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Para** | Sim | <*your-email-address*> | O destinatário do email, que pode ser seu endereço de email para fins de teste. Este exemplo usa o email fictício, `sophiaowen@fabrikam.com` . |
   | **Assunto** | Sim | `An email from your example workflow` | O assunto do email |
   | **Corpo** | Sim | `Hello from your example workflow!` | O conteúdo do corpo do email |
   ||||

   > [!NOTE]
   > Se você quiser fazer alterações no painel de detalhes na guia **configurações**, **resultado estático** ou **executar após** , certifique-se de selecionar **concluído** para confirmar essas alterações antes de alternar as guias ou alterar o foco para o designer. Caso contrário, Visual Studio Code não manterá suas alterações. Para obter mais informações, consulte a [página problemas conhecidos da visualização pública de aplicativos lógicos no GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. No designer, selecione **salvar**.

> [!IMPORTANT]
> Para executar localmente um fluxo de trabalho que usa um gatilho ou ações baseadas em webhook, como o [gatilho ou a ação de webhook http interno](../connectors/connectors-native-webhook.md), você deve habilitar esse recurso [Configurando o encaminhamento para a URL de retorno de chamada do webhook](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Habilitar WebHooks em execução localmente

Quando você usa um gatilho ou uma ação baseada em webhook, como **webhook http**, com um aplicativo lógico em execução no Azure, o tempo de execução dos aplicativos lógicos assina o ponto de extremidade de serviço gerando e registrando uma URL de retorno de chamada com esse ponto de extremidade. O gatilho ou a ação aguarda o ponto de extremidade de serviço chamar a URL. No entanto, quando você está trabalhando no Visual Studio Code, a URL de retorno de chamada gerada começa com `http://localhost:7071/...` . Essa URL é para o servidor localhost, que é privado para que o ponto de extremidade de serviço não possa chamar essa URL.

Para executar localmente gatilhos e ações baseados em webhook no Visual Studio Code, você precisa configurar uma URL pública que expõe o servidor localhost e encaminha com segurança as chamadas do ponto de extremidade de serviço para a URL de retorno de chamada do webhook. Você pode usar um serviço de encaminhamento e uma ferramenta como o [**ngrok**](https://ngrok.com/), que abre um túnel http para sua porta localhost ou você pode usar sua própria ferramenta.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Configurar o encaminhamento de chamada usando **ngrok**

1. [Inscreva-se em uma conta do **ngrok**](https://dashboard.ngrok.com/signup) se você não tiver uma. Caso contrário, [entre em sua conta](https://dashboard.ngrok.com/login).

1. Obtenha seu token de autenticação pessoal, que o cliente do **ngrok** precisa para se conectar e autenticar o acesso à sua conta.

   1. Para localizar sua [página de token de autenticação](https://dashboard.ngrok.com/auth/your-authtoken), no menu do painel de sua conta, expanda **autenticação** e selecione **seu authToken**.

   1. Na caixa **seu authToken** , copie o token para um local seguro.

1. Na página de download do [ **ngrok**](https://ngrok.com/download) ou [no painel da sua conta](https://dashboard.ngrok.com/get-started/setup), baixe a versão do **ngrok** que você deseja e extraia o arquivo. zip. Para obter mais informações, consulte [etapa 1: descompactar para instalar](https://ngrok.com/download).

1. Em seu computador, abra a ferramenta de prompt de comando. Navegue até o local onde você tem o arquivo de **ngrok.exe** .

1. Conecte o cliente **ngrok** à sua conta do **ngrok** executando o comando a seguir. Para obter mais informações, consulte [etapa 2: conectar sua conta](https://ngrok.com/download).

   `ngrok authtoken <your_auth_token>`

1. Abra o túnel HTTP para a porta localhost 7071 executando o comando a seguir. Para obter mais informações, consulte [Step 3: acionar](https://ngrok.com/download).

   `ngrok http 7071`

1. Na saída, localize a seguinte linha:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Copie e salve a URL que tem este formato: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Configurar a URL de encaminhamento nas configurações do aplicativo

1. No Visual Studio Code, no designer, adicione o gatilho ou ação **http + webhook** .

1. Quando o prompt for exibido para o local do ponto de extremidade do host, insira a URL de encaminhamento (redirecionamento) que você criou anteriormente.

   > [!NOTE]
   > Ignorar o prompt faz com que um aviso pareça que você deve fornecer a URL de encaminhamento, portanto, selecione **Configurar** e insira a URL. Depois de concluir esta etapa, o prompt não reaparecerá para gatilhos ou ações de webhook subsequentes que você possa adicionar.
   >
   > Para fazer com que o prompt reapareça, no nível raiz do seu projeto, abra o **local.settings.jsno** menu de atalho do arquivo e selecione **Configurar ponto de extremidade de redirecionamento do webhook**. O prompt agora é exibido para que você possa fornecer a URL de encaminhamento.

   Visual Studio Code adiciona a URL de encaminhamento ao **local.settings.jsno** arquivo na pasta raiz do projeto. No `Values` objeto, a propriedade chamada `Workflows.WebhookRedirectHostUri` agora aparece e é definida como a URL de encaminhamento, por exemplo:
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

Na primeira vez que você inicia uma sessão de depuração local ou executa o fluxo de trabalho sem depuração, o tempo de execução dos aplicativos lógicos registra o fluxo de trabalho com o ponto de extremidade de serviço e assina esse ponto de extremidade para notificar as operações de webhook. Na próxima vez em que o fluxo de trabalho for executado, o tempo de execução não será registrado ou reassinado porque o registro de assinatura já existe no armazenamento local.

Quando você interrompe a sessão de depuração para uma execução de fluxo de trabalho que usa localmente executar gatilhos ou ações baseadas em webhook, os registros de assinatura existentes não são excluídos. Para cancelar o registro, você precisa remover manualmente ou excluir os registros de assinatura.

> [!NOTE]
> Depois que o fluxo de trabalho começar a ser executado, a janela do terminal poderá mostrar erros como este exemplo:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Nesse caso, abra o **local.settings.jsno** arquivo na pasta raiz do projeto e verifique se a propriedade está definida como `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Gerenciar pontos de interrupção para depuração

Antes de executar e testar o fluxo de trabalho do aplicativo lógico iniciando uma sessão de depuração, você pode definir [pontos de interrupção](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) dentro do **workflow.jsno** arquivo para cada fluxo de trabalho. Nenhuma outra configuração é necessária. 

Neste momento, os pontos de interrupção têm suporte apenas para ações, não para gatilhos. Cada definição de ação tem estes locais de ponto de interrupção:

* Defina o ponto de interrupção inicial na linha que mostra o nome da ação. Quando esse ponto de interrupção atinge durante a sessão de depuração, você pode revisar as entradas da ação antes que elas sejam avaliadas.

* Defina o ponto de interrupção final na linha que mostra a chave de fechamento da ação (**}**). Quando esse ponto de interrupção atinge durante a sessão de depuração, você pode examinar os resultados da ação antes de concluir a execução da ação.

Para adicionar um ponto de interrupção, siga estas etapas:

1. Abra o **workflow.jsno** arquivo para o fluxo de trabalho que você deseja depurar.

1. Na linha em que você deseja definir o ponto de interrupção, na coluna à esquerda, selecione dentro dessa coluna. Para remover o ponto de interrupção, selecione esse ponto de interrupção.

   Quando você inicia a sessão de depuração, o modo de exibição de execução aparece no lado esquerdo da janela de código, enquanto a barra de ferramentas de depuração aparece perto da parte superior.

   > [!NOTE]
   > Se a exibição de execução não aparecer automaticamente, pressione Ctrl + Shift + D.

1. Para examinar as informações disponíveis quando um ponto de interrupção atinge, no modo de exibição de execução, examine o painel **variáveis** .

1. Para continuar a execução do fluxo de trabalho, na barra de ferramentas depurar, selecione **continuar** (botão reproduzir).

Você pode adicionar e remover pontos de interrupção a qualquer momento durante a execução do fluxo de trabalho. No entanto, se você atualizar o **workflow.jsno** arquivo depois que a execução for iniciada, os pontos de interrupção não atualizarão automaticamente. Para atualizar os pontos de interrupção, reinicie o aplicativo lógico.

Para obter informações gerais, consulte [pontos de interrupção-Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Executar, testar e depurar localmente

Para testar seu aplicativo lógico, siga estas etapas para iniciar uma sessão de depuração e localize a URL para o ponto de extremidade que é criado pelo gatilho de solicitação. Você precisa dessa URL para que possa enviar posteriormente uma solicitação para esse ponto de extremidade.

1. Para depurar um fluxo de trabalho sem estado com mais facilidade, você pode [habilitar o histórico de execução para esse fluxo de trabalho](#enable-run-history-stateless).

1. Na barra de atividade do Visual Studio Code, abra o menu **executar** e selecione **Iniciar Depuração** (F5).

   A janela do **terminal** é aberta para que você possa examinar a sessão de depuração.

   > [!NOTE]
   > Se você receber o erro **"o erro existe depois de executar o preLaunchTask ' generateDebugSymbols '"**, consulte a seção de solução de problemas, [falha na inicialização da sessão de depuração](#debugging-fails-to-start).

1. Agora, localize a URL de retorno de chamada para o ponto de extremidade no gatilho de solicitação.

   1. Reabra o painel do Explorer para que você possa exibir seu projeto.

   1. Na **workflow.js** menu de atalho do arquivo, selecione **visão geral**.

      ![Captura de tela que mostra o painel do Explorer e a janela de atalho para a workflow.jsno arquivo com a "visão geral" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Localize o valor da **URL de retorno de chamada** , que é semelhante a esta URL para o gatilho de solicitação de exemplo:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Captura de tela que mostra a página Visão geral do fluxo de trabalho com URL de retorno de chamada](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Para testar a URL de retorno de chamada disparando o fluxo de trabalho do aplicativo lógico, abra o [postmaster](https://www.postman.com/downloads/) ou sua ferramenta preferida para criar e enviar solicitações.

   Este exemplo continua usando o postmaster. Para obter mais informações, consulte [introdução do postmaster](https://learning.postman.com/docs/getting-started/introduction/).

   1. Na barra de ferramentas do postmaster, selecione **novo**.

      ![Captura de tela que mostra o postmaster com o botão novo selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. No painel **criar novo** , em **blocos de construção**, selecione **solicitação**.

   1. Na janela **salvar solicitação** , em **nome da solicitação**, forneça um nome para a solicitação, por exemplo, `Test workflow trigger` .

   1. Em **selecionar uma coleção ou pasta para salvar**, selecione **criar coleção**.

   1. Em **todas as coleções**, forneça um nome para a coleção a ser criada para organizar suas solicitações, pressione Enter e selecione **salvar para <*nome* > da coleção**. Este exemplo usa `Logic Apps requests` como o nome da coleção.

      O painel de solicitação do postmaster é aberto para que você possa enviar uma solicitação para a URL de retorno de chamada para o gatilho de solicitação.

      ![Captura de tela que mostra o postmaster com o painel de solicitação aberto](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Retornar para Visual Studio Code. na página Visão geral do fluxo de trabalho, copie o valor da propriedade **URL de retorno de chamada** .

   1. Retornar ao postmaster. No painel de solicitação, próximo à lista de métodos, que atualmente mostra **Get** como o método de solicitação padrão, Cole a URL de retorno de chamada que você copiou anteriormente na caixa endereço e selecione **Enviar**.

      ![Captura de tela que mostra o postmaster e a URL de retorno de chamada na caixa de endereço com o botão enviar selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      O fluxo de trabalho do aplicativo lógico de exemplo envia um email que é semelhante a este exemplo:

      ![Captura de tela que mostra o email do Outlook, conforme descrito no exemplo](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Em Visual Studio Code, retorne à página de visão geral do fluxo de trabalho.

   Se você criou um fluxo de trabalho com estado, após a solicitação que você enviou dispara o fluxo de trabalho, a página Visão geral mostra o status e o histórico de execução do fluxo de trabalho.

   > [!TIP]
   > Se o status de execução não for exibido, tente atualizar a página Visão geral selecionando **Atualizar**. Não acontece nenhuma execução para um gatilho que é ignorado devido a critérios não cumpridos ou não encontrar dados.

   ![Captura de tela que mostra a página Visão geral do fluxo de trabalho com o status de execução e o histórico](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

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

1. Para examinar os status de cada etapa em uma execução específica e as entradas e saídas da etapa, selecione o botão de reticências (**...**) para essa execução e selecione **Mostrar execução**.

   ![Captura de tela que mostra a linha do histórico de execução do fluxo de trabalho com o botão de reticências e "mostrar execução" selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code abre a exibição monitoramento e mostra o status de cada etapa na execução.

   ![Captura de tela que mostra cada etapa na execução do fluxo de trabalho e seu status](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Se uma execução falhar e uma etapa na exibição de monitoramento mostrar o `400 Bad Request` erro, esse problema poderá resultar de um nome de gatilho mais longo ou de um nome de ação que faz com que o Uniform Resource Identifier (URI) subjacente exceda o limite de caracteres padrão. Para obter mais informações, consulte ["400 solicitação inadequada"](#400-bad-request).

   Estes são os possíveis status que cada etapa do fluxo de trabalho pode ter:

   | Status da ação | ícone | Descrição |
   |---------------|------|-------------|
   | **Anulado** | ![Ícone do status de ação "abortado"][aborted-icon] | A ação foi interrompida ou não foi concluída devido a problemas externos, por exemplo, uma interrupção do sistema ou uma assinatura do Azure enlapsada. |
   | **Cancelar** | ![Ícone do status de ação "cancelado"][cancelled-icon] | A ação estava em execução, mas recebeu uma solicitação para cancelar. |
   | **Falha** | ![Ícone do status da ação "falha"][failed-icon] | Falha na ação. |
   | **Executando** | ![Ícone para o status de ação "em execução"][running-icon] | A ação está em execução no momento. |
   | **Ignorado** | ![Ícone do status de ação "ignorado"][skipped-icon] | A ação foi ignorada porque a ação imediatamente anterior falhou. Uma ação tem uma `runAfter` condição que requer que a ação anterior seja concluída com êxito antes que a ação atual possa ser executada. |
   | **Êxito** | ![Ícone do status de ação "êxito"][succeeded-icon] | A ação foi bem-sucedida. |
   | **Êxito com novas tentativas** | ![Ícone para o status de ação "êxito com novas tentativas"][succeeded-with-retries-icon] | A ação foi bem-sucedida, mas somente após uma ou mais tentativas. Para examinar o histórico de repetição, na exibição detalhes do histórico de execuções, selecione essa ação para que você possa exibir as entradas e saídas. |
   | **Tempo limite atingido** | ![Ícone do status de ação "tempo limite"][timed-out-icon] | A ação foi interrompida devido ao limite de tempo limite especificado pelas configurações dessa ação. |
   | **Aguardando** | ![Ícone do status de ação "aguardando"][waiting-icon] | Aplica-se a uma ação de webhook que está aguardando uma solicitação de entrada de um chamador. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Para examinar as entradas e saídas de cada etapa, selecione a etapa que você deseja inspecionar.

   ![Captura de tela que mostra o status de cada etapa no fluxo de trabalho mais as entradas e saídas na ação "enviar um email" expandida](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Para examinar melhor as entradas e saídas brutas para essa etapa, selecione **Mostrar entradas brutas** ou **Mostrar saídas brutas**.

1. Para interromper a sessão de depuração, no menu **executar** , selecione **parar depuração** (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Retornar uma resposta

Para retornar uma resposta ao chamador que enviou uma solicitação ao seu aplicativo lógico, você pode usar a [ação de resposta](../connectors/connectors-native-reqres.md) interna para um fluxo de trabalho que começa com o gatilho de solicitação.

1. No designer de fluxo de trabalho, na ação **enviar um email** , selecione **nova etapa**.

   O prompt **escolher uma operação** aparece no designer e o **painel Adicionar uma ação** é reaberto para que você possa selecionar a próxima ação.

1. No painel **Adicionar uma ação** , na caixa de pesquisa **escolher uma ação** , certifique-se de que o **interno** está selecionado. Na caixa de pesquisa, insira `response` e selecione a ação de **resposta** .

   ![Captura de tela que mostra o designer de fluxo de trabalho com a ação de resposta selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Quando a ação de **resposta** aparece no designer, o painel detalhes da ação é aberto automaticamente.

   ![Captura de tela que mostra o designer de fluxo de trabalho com o painel de detalhes da ação "resposta" aberto e a propriedade "corpo" definida como o valor da propriedade "corpo" da ação "enviar um email".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Na guia **parâmetros** , forneça as informações necessárias para a função que você deseja chamar.

   Este exemplo retorna o valor da propriedade **Body** que é a saída da ação **enviar um email** .

   1. Clique dentro da caixa de propriedade **corpo** para que a lista de conteúdo dinâmico apareça e mostre os valores de saída disponíveis do gatilho anterior e as ações no fluxo de trabalho.

      ![Captura de tela que mostra o painel de detalhes da ação "resposta" com o ponteiro do mouse dentro da propriedade "corpo" para que a lista de conteúdo dinâmico seja exibida.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Na lista conteúdo dinâmico, em **enviar um email**, selecione **corpo**.

      ![Captura de tela que mostra a lista de conteúdo dinâmico aberto. Na lista, no cabeçalho "enviar um email", o valor de saída "corpo" é selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Quando terminar, a propriedade **Body** da ação de resposta será agora definida como o valor de saída do **corpo** da ação **enviar um email** .

      ![Captura de tela que mostra o status de cada etapa no fluxo de trabalho mais as entradas e saídas na ação expandida de "resposta".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. No designer, selecione **salvar**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Testar novamente seu aplicativo lógico

Depois de fazer atualizações em seu aplicativo lógico, você pode executar outro teste executando novamente o depurador no Visual Studio e enviando outra solicitação para disparar seu aplicativo lógico atualizado, semelhante às etapas em [Executar, testar e depurar localmente](#run-test-debug-locally).

1. Na barra de atividade do Visual Studio Code, abra o menu **executar** e selecione **Iniciar Depuração** (F5).

1. No postmaster ou em sua ferramenta para criar e enviar solicitações, envie outra solicitação para disparar seu fluxo de trabalho.

1. Se você criou um fluxo de trabalho com estado, na página Visão geral do fluxo de trabalho, verifique o status da execução mais recente. Para exibir o status, as entradas e as saídas de cada etapa na execução, selecione o botão de reticências (**...**) para essa execução e selecione **Mostrar execução**.

   Por exemplo, aqui está o status passo a passo para uma execução após a atualização do fluxo de trabalho de exemplo com a ação de resposta.

   ![Captura de tela que mostra o status de cada etapa no fluxo de trabalho atualizado mais as entradas e saídas na ação expandida de "resposta".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Para interromper a sessão de depuração, no menu **executar** , selecione **parar depuração** (Shift + F5).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Localizar nomes de domínio para acesso ao firewall

Antes de implantar e executar o fluxo de trabalho do aplicativo lógico no portal do Azure, se o ambiente tiver requisitos estritos de rede ou firewalls que limitem o tráfego, você precisará configurar permissões para qualquer conexão de gatilho ou ação que exista em seu fluxo de trabalho.

Para localizar os FQDNs (nomes de domínio totalmente qualificados) para essas conexões, siga estas etapas:

1. No seu projeto de aplicativo lógico, abra o **connections.jsno** arquivo, que é criado depois de adicionar o primeiro gatilho ou ação baseado em conexão ao seu fluxo de trabalho e localizar o `managedApiConnections` objeto.

1. Para cada conexão que você criou, localize, copie e salve o `connectionRuntimeUrl` valor da propriedade em um local seguro para que você possa configurar o firewall com essas informações.

   Este exemplo **connections.jsno** arquivo contém duas conexões, uma conexão AS2 e uma conexão do Office 365 com estes `connectionRuntimeUrl` valores:

   * AS2 `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Implantar no Azure

No Visual Studio Code, você pode publicar diretamente seu projeto no Azure, que implanta seu aplicativo lógico usando o novo tipo de recurso de **aplicativo lógico (versão prévia)** . Semelhante ao recurso de aplicativo de funções no Azure Functions, a implantação para esse novo tipo de recurso requer que você selecione um [plano de hospedagem e tipo de preço](../app-service/overview-hosting-plans.md), que pode ser configurado durante a implantação. Para obter mais informações sobre planos de hospedagem e preços, consulte estes tópicos:

* [Escalar verticalmente um serviço de Azure App](../app-service/manage-scale-up.md)
* [Escala e hospedagem do Azure Functions](../azure-functions/functions-scale.md)

Você pode publicar seu aplicativo lógico como um novo recurso, que cria automaticamente todos os recursos necessários, como uma [conta de armazenamento do Azure, semelhante aos requisitos do aplicativo de funções](../azure-functions/storage-considerations.md). Ou então, você pode publicar seu aplicativo lógico em um recurso de aplicativo lógico implantado anteriormente **(versão prévia)** , que substitui esse aplicativo lógico.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publicar em um novo recurso de aplicativo lógico (versão prévia)

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure.

1. Na barra de ferramentas **do painel Azure: aplicativos lógicos (visualização)** , selecione **implantar no aplicativo lógico**.

   ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e a barra de ferramentas do painel com "implantar no aplicativo lógico" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Se solicitado, selecione a assinatura do Azure a ser usada para a implantação do aplicativo lógico.

1. Na lista que Visual Studio Code é aberta, selecione uma destas opções:

   * **Criar novo aplicativo lógico (versão prévia) no Azure** (rápido)
   * **Criar novo aplicativo lógico (versão prévia) no Azure avançado**
   * Um recurso de **aplicativo lógico** implantado anteriormente (versão prévia), se existir algum

   Este exemplo continua com **criar novo aplicativo lógico (versão prévia) no Azure avançado**.

   ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" com uma lista com "criar novo aplicativo lógico (visualização) no Azure" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Para criar seu novo recurso de **aplicativo lógico (versão prévia)** , siga estas etapas:

   1. Forneça um nome global exclusivo para seu novo aplicativo lógico, que é o nome a ser usado para o recurso de **aplicativo lógico (versão prévia)** . Este exemplo usa o `Fabrikam-Workflows-App`.

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para fornecer um nome para o novo aplicativo lógico a ser criado.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selecione um [plano de hospedagem](../app-service/overview-hosting-plans.md) para seu novo aplicativo lógico, o [ **plano do serviço de aplicativo** (dedicado)](../azure-functions/dedicated-plan.md) ou [**Premium**](../azure-functions/functions-premium-plan.md).

      > [!IMPORTANT]
      > Os planos de consumo não têm suporte nem estão disponíveis para este tipo de recurso. O plano selecionado afeta os recursos e as camadas de preço mais tarde disponíveis para você. Para obter mais informações, consulte estes tópicos: 
      >
      > * [Escala e hospedagem do Azure Functions](../azure-functions/functions-scale.md)
      > * [Detalhes de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Por exemplo, o plano Premium fornece acesso a recursos de rede, como conectar e integrar-se de forma privada com redes virtuais do Azure, semelhante a Azure Functions quando você cria e implanta seus aplicativos lógicos. 
      > Para obter mais informações, consulte estes tópicos:
      > 
      > * [Opções de rede do Azure Functions](../azure-functions/functions-networking-options.md)
      > * [Aplicativos lógicos do Azure executando possibilidades de rede em qualquer lugar com os aplicativos lógicos do Azure Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      Este exemplo usa o **plano do serviço de aplicativo**.

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para selecionar "plano do serviço de aplicativo" ou "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Crie um novo plano do serviço de aplicativo ou selecione um plano existente. Este exemplo seleciona **criar novo plano do serviço de aplicativo**.

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para "criar novo plano do serviço de aplicativo" ou selecionar um plano do serviço de aplicativo existente.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Forneça um nome para o plano do serviço de aplicativo e, em seguida, selecione um [tipo de preço](../app-service/overview-hosting-plans.md) para o plano. Este exemplo seleciona o plano **gratuito F1** .

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para selecionar um tipo de preço.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Para obter o desempenho ideal, localize e selecione o mesmo grupo de recursos que o seu projeto para a implantação.

      > [!NOTE]
      > Embora você possa criar ou usar um grupo de recursos diferente, isso pode afetar o desempenho. Se você criar ou escolher um grupo de recursos diferente, mas cancelar após o prompt de confirmação aparecer, sua implantação também será cancelada.

   1. Para fluxos de trabalho com estado, selecione **criar nova conta de armazenamento** ou uma conta de armazenamento existente.

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para criar ou selecionar uma conta de armazenamento.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Se a criação e as configurações de implantação do aplicativo lógico dão suporte ao uso de [Application insights](../azure-monitor/app/app-insights-overview.md), você pode opcionalmente habilitar o log e o rastreamento de diagnóstico para seu aplicativo lógico. Isso pode ser feito quando você implanta seu aplicativo lógico do Visual Studio Code ou após a implantação. Você precisa ter uma instância de Application Insights, mas você pode criar esse recurso com [antecedência](../azure-monitor/app/create-workspace-resource.md), ao implantar seu aplicativo lógico ou após a implantação.

      Para habilitar o log e o rastreamento agora, siga estas etapas:

      1. Selecione um recurso de Application Insights existente ou **crie um novo recurso de Application insights**.

      1. Na [portal do Azure](https://portal.azure.com), vá para o recurso de Application insights.

      1. No menu de recursos, selecione **visão geral**. Localize e copie o valor da **chave de instrumentação** .

      1. No Visual Studio Code, na pasta raiz do projeto, abra o **local.settings.jsno** arquivo.

      1. No `Values` objeto, adicione a `APPINSIGHTS_INSTRUMENTATIONKEY` propriedade e defina o valor para a chave de instrumentação, por exemplo:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Você pode verificar se os nomes do gatilho e da ação aparecem corretamente em sua instância de Application Insights.
         >
         > 1. Na portal do Azure, vá para o recurso de Application Insights.
         >
         > 2. No menu recurso de recurso, em **investigar**, selecione **mapa de aplicativos**.
         >
         > 3. Examine os nomes de operação que aparecem no mapa.
         >
         > Algumas solicitações de entrada de gatilhos internos podem aparecer como duplicatas no mapa do aplicativo. 
         > Em vez de usar o `WorkflowName.ActionName` formato, essas duplicatas usam o nome do fluxo de trabalho como o nome da operação e são originadas no host Azure functions.

      1. Em seguida, você pode, opcionalmente, ajustar o nível de severidade para os dados de rastreamento que seu aplicativo lógico coleta e envia para sua instância de Application Insights.

         Cada vez que um evento relacionado ao fluxo de trabalho acontece, como quando um fluxo de trabalho é disparado ou quando uma ação é executada, o tempo de execução emite vários rastreamentos. Esses rastreamentos abordam o tempo de vida do fluxo de trabalho e incluem, mas não se limitam a, os seguintes tipos de evento:

         * Atividade de serviço, como iniciar, parar e erros.
         * Atividade de trabalhos e Dispatcher.
         * Atividade de fluxo de trabalho, como gatilho, ação e execução.
         * Atividade de solicitação de armazenamento, como êxito ou falha.
         * Atividade de solicitação HTTP, como entrada, saída, êxito e falha.
         * Qualquer rastreamento de desenvolvimento, como mensagens de depuração.

         Cada tipo de evento é atribuído a um nível de severidade. Por exemplo, o `Trace` nível captura as mensagens mais detalhadas, enquanto o `Information` nível captura a atividade geral no fluxo de trabalho, como quando o aplicativo lógico, o fluxo de trabalho, o gatilho e as ações são iniciados e interrompidos. Esta tabela descreve os níveis de severidade e seus tipos de rastreamento:

         | Nível de severidade | Tipo de rastreamento |
         |----------------|------------|
         | Crítico | Logs que descrevem uma Falha irrecuperável em seu aplicativo lógico. |
         | Depurar | Logs que você pode usar para investigação durante o desenvolvimento, por exemplo, chamadas HTTP de entrada e saída. |
         | Erro | Logs que indicam uma falha na execução do fluxo de trabalho, mas não uma falha geral em seu aplicativo lógico. |
         | Informações do | Logs que acompanham a atividade geral em seu aplicativo lógico ou fluxo de trabalho, por exemplo: <p><p>-Quando um gatilho, uma ação ou uma execução é iniciada e termina. <br>-Quando o aplicativo lógico é iniciado ou encerrado. |
         | Trace | Logs que contêm as mensagens mais detalhadas, por exemplo, solicitações de armazenamento ou atividade do Dispatcher, além de todas as mensagens relacionadas à atividade de execução do fluxo de trabalho. |
         | Aviso | Logs que realçam um estado anormal em seu aplicativo lógico, mas não impedem sua execução. |
         |||

         Para definir o nível de severidade, no nível raiz do seu projeto, abra o **host.jsno** arquivo e localize o `logging` objeto. Esse objeto controla a filtragem de log para todos os fluxos de trabalho em seu aplicativo lógico e segue o [layout de ASP.NET Core para a filtragem de tipo de log](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Se o `logging` objeto não contiver um `logLevel` objeto que inclua a `Host.Triggers.Workflow` propriedade, adicione esses itens. Defina a propriedade para o nível de severidade para o tipo de rastreamento desejado, por exemplo:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Quando você terminar as etapas de implantação, Visual Studio Code começará a criar e implantar os recursos necessários para publicar seu aplicativo lógico.

1. Para revisar e monitorar o processo de implantação, no menu **Exibir** , selecione **saída**. Na lista da barra de ferramentas da janela saída, selecione **aplicativos lógicos do Azure**.

   ![Captura de tela que mostra a janela de saída com o "aplicativo lógico do Azure" selecionado na lista de barras de ferramentas, juntamente com o progresso e os status da implantação.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Quando Visual Studio Code concluir a implantação do aplicativo lógico no Azure, a seguinte mensagem será exibida:

   ![Captura de tela que mostra uma mensagem informando que a implantação para o Azure foi concluída com êxito.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Parabéns, seu aplicativo lógico agora está ativo no Azure e habilitado por padrão.

Em seguida, você pode aprender a executar estas tarefas:

* [Adicione um fluxo de trabalho em branco ao seu projeto](#add-workflow-existing-project).

* [Gerencie aplicativos lógicos implantados no Visual Studio Code](#manage-deployed-apps-vs-code) ou usando o [portal do Azure](#manage-deployed-apps-portal).

* [Habilite o histórico de execução em fluxos de trabalho sem estado](#enable-run-history-stateless).

* [Habilite o modo de exibição de monitoramento no portal do Azure para um aplicativo lógico implantado](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Adicionar fluxo de trabalho em branco ao projeto

Você pode ter vários fluxos de trabalho em seu projeto de aplicativo lógico. Para adicionar um fluxo de trabalho em branco ao seu projeto, siga estas etapas:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure.

1. No painel do Azure, ao lado de **Azure: aplicativos lógicos (versão prévia)**, selecione **criar fluxo de trabalho** (ícone para aplicativos lógicos do Azure).

1. Selecione o tipo de fluxo de trabalho que você deseja **Adicionar: com estado ou** **sem estado**

1. Forneça um nome para o fluxo de trabalho.

Quando terminar, uma nova pasta de fluxo de trabalho será exibida em seu projeto junto com um **workflow.jsno** arquivo para a definição de fluxo de trabalho.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Gerenciar aplicativos lógicos implantados no Visual Studio Code

No Visual Studio Code, você pode exibir todos os aplicativos lógicos implantados em sua assinatura do Azure, sejam eles os **aplicativos lógicos** originais ou o tipo de recurso **aplicativo lógico (versão prévia)** e selecionar tarefas que ajudam a gerenciar esses aplicativos lógicos. No entanto, para acessar ambos os tipos de recurso, você precisa dos **aplicativos lógicos do Azure** e das extensões de **aplicativos lógicos do Azure (versão prévia)** para Visual Studio Code.

1. Na barra de ferramentas à esquerda, selecione o ícone do Azure. No painel **Azure: aplicativos lógicos (visualização)** , expanda sua assinatura, que mostra todos os aplicativos lógicos implantados para essa assinatura.

1. Abra o aplicativo lógico que você deseja gerenciar. No menu de atalho do aplicativo lógico, selecione a tarefa que você deseja executar.

   Por exemplo, você pode selecionar tarefas como parar, iniciar, reiniciar ou excluir seu aplicativo lógico implantado.

   ![Captura de tela que mostra Visual Studio Code com o painel de extensão "aplicativos lógicos do Azure (visualização)" aberto e o fluxo de trabalho implantado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Para exibir todos os fluxos de trabalho no aplicativo lógico, expanda seu aplicativo lógico e, em seguida, expanda o nó **fluxos de trabalho** .

1. Para exibir um fluxo de trabalho específico, abra o menu de atalho do fluxo de trabalho e selecione **abrir no designer**, que abre o fluxo de trabalho no modo somente leitura.

   Para editar o fluxo de trabalho, você tem estas opções:

   * No Visual Studio Code, abra oworkflow.jsdo projeto **no** arquivo no designer de fluxo de trabalho, faça suas edições e reimplante seu aplicativo lógico no Azure.

   * Na portal do Azure, [Localize e abra seu aplicativo lógico](#manage-deployed-apps-portal). Localize, edite e salve o fluxo de trabalho.

1. Para abrir o aplicativo lógico implantado no portal do Azure, abra o menu de atalho do aplicativo lógico e selecione **abrir no portal**.

   O portal do Azure é aberto no navegador, você entra no portal automaticamente se estiver conectado a Visual Studio Code e mostra seu aplicativo lógico.

   ![Captura de tela que mostra a página de portal do Azure para seu aplicativo lógico no Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Você também pode entrar separadamente para o portal do Azure, use a caixa de pesquisa do portal para localizar seu aplicativo lógico e, em seguida, selecione seu aplicativo lógico na lista de resultados.

   ![Captura de tela que mostra a portal do Azure e a barra de pesquisa com os resultados da pesquisa para o aplicativo lógico implantado, que aparece selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Gerenciar aplicativos lógicos implantados no portal

No portal do Azure, você pode exibir todos os aplicativos lógicos implantados que estão em sua assinatura do Azure, sejam eles o tipo de recurso **aplicativos lógicos** originais ou o tipo de recurso **aplicativo lógico (versão prévia)** . Atualmente, cada tipo de recurso é organizado e gerenciado como categorias separadas no Azure. Para localizar aplicativos lógicos que têm o tipo de recurso **aplicativo lógico (versão prévia)** , siga estas etapas:

1. Na caixa de pesquisa portal do Azure, digite `logic app preview` . Quando a lista de resultados aparecer, em **Serviços**, selecione **aplicativo lógico (versão prévia)**.

   ![Captura de tela que mostra a caixa de pesquisa portal do Azure com o texto de pesquisa "visualização do aplicativo lógico".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. No painel **aplicativo lógico (visualização)** , localize e selecione o aplicativo lógico que você implantou de Visual Studio Code.

   ![Captura de tela que mostra os recursos de portal do Azure e aplicativo lógico (versão prévia) implantados no Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   O portal do Azure abre a página de recursos individuais para o aplicativo lógico selecionado.

   ![Captura de tela que mostra a página de recursos do fluxo de trabalho do aplicativo lógico no portal do Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Para exibir os fluxos de trabalho neste aplicativo lógico, no menu do aplicativo lógico, selecione **fluxos de trabalho**.

   O painel **fluxos de trabalho** mostra todos os fluxos de trabalho no aplicativo lógico atual. Este exemplo mostra o fluxo de trabalho que você criou no Visual Studio Code.

   ![Captura de tela que mostra uma página de recursos "aplicativo lógico (visualização)" com o painel "fluxos de trabalho" aberto e o fluxo de trabalho implantado](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Para exibir um fluxo de trabalho, no painel **fluxos de trabalho** , selecione esse fluxo de trabalho.

   O painel fluxo de trabalho é aberto e mostra mais informações e tarefas que podem ser executadas nesse fluxo de trabalho.

   Por exemplo, para exibir as etapas no fluxo de trabalho, selecione **Designer**.

   ![Captura de tela que mostra o painel "visão geral" do fluxo de trabalho selecionado, enquanto o menu fluxo de trabalho mostra o comando "designer" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   O designer de fluxo de trabalho é aberto e mostra o fluxo de trabalho que você criou Visual Studio Code. Agora você pode fazer alterações nesse fluxo de trabalho na portal do Azure.

   ![Captura de tela que mostra o Workflow Designer e o fluxo de trabalho implantados de Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Adicionar outro fluxo de trabalho no portal

Por meio do portal do Azure, você pode adicionar fluxos de trabalho em branco a um recurso de **aplicativo lógico (versão prévia)** implantado de Visual Studio Code e criar esses fluxos de trabalho no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com), localize e selecione o recurso aplicativo lógico implantado **(versão prévia)** .

1. No menu do aplicativo lógico, selecione **fluxos de trabalho**. No painel **fluxos de trabalho** , selecione **Adicionar**.

   ![Captura de tela que mostra o painel "fluxos de trabalho" do aplicativo lógico selecionado e a barra de ferramentas com o comando "Adicionar" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. No **novo painel fluxo de trabalho** , forneça um nome para o fluxo de trabalho. Selecione a opção com **estado** ou **sem monitoração de estado** **>** .

   Depois que o Azure implantar o novo fluxo de trabalho, que aparece no painel **fluxos de trabalho** , selecione esse fluxo de trabalho para que você possa gerenciar e executar outras tarefas, como abrir o designer ou a exibição de código.

   ![Captura de tela que mostra o fluxo de trabalho selecionado com opções de gerenciamento e revisão.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Por exemplo, abrir o designer para um novo fluxo de trabalho mostra uma tela em branco. Agora você pode criar esse fluxo de trabalho no portal do Azure.

   ![Captura de tela que mostra o designer de fluxo de trabalho e um fluxo de trabalho em branco.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Habilitar o histórico de execuções para fluxos de trabalho sem estado

Para depurar um fluxo de trabalho sem estado com mais facilidade, você pode habilitar o histórico de execução para esse fluxo de trabalho e, em seguida, desabilitar o histórico de execução quando terminar. Siga estas etapas para Visual Studio Code ou, se estiver trabalhando no portal do Azure, confira [criar fluxos de trabalho com e sem estado no portal do Azure](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. Em seu projeto de Visual Studio Code, expanda a pasta **Workflow-designtime** e abra o **local.settings.jsno** arquivo.

1. Adicione a `Workflows.{yourWorkflowName}.operationOptions` propriedade e defina o valor como `WithStatelessRunHistory` , por exemplo:

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS ou Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Para desabilitar o histórico de execuções quando terminar, defina a `Workflows.{yourWorkflowName}.OperationOptions` propriedade como `None` ou exclua a propriedade e seu valor.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Habilitar o modo de exibição de monitoramento no portal do Azure

Depois de implantar um recurso de **aplicativo lógico (versão prévia)** de Visual Studio Code para o Azure, você pode examinar qualquer histórico de execução disponível e detalhes de um fluxo de trabalho nesse recurso usando o portal do Azure e a experiência de **Monitor** para esse fluxo de trabalho. No entanto, primeiro você precisa habilitar a funcionalidade de exibição de **Monitor** nesse recurso de aplicativo lógico.

1. Na [portal do Azure](https://portal.azure.com), localize e selecione o recurso aplicativo lógico implantado **(versão prévia)** .

1. No menu desse recurso, em **API**, selecione **CORS**.

1. No painel **CORS** , em **origens permitidas**, adicione o caractere curinga (*).

1. Quando terminar, na barra de ferramentas **CORS** , selecione **salvar**.

   ![Captura de tela que mostra a portal do Azure com um recurso de aplicativo lógico implantado (versão prévia). No menu de recursos, "CORS" é selecionado com uma nova entrada para "origens permitidas" definida como o caractere curinga "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Habilitar ou abrir Application Insights após a implantação

Durante a execução do fluxo de trabalho, seu aplicativo lógico emite telemetria junto com outros eventos. Você pode usar essa telemetria para obter melhor visibilidade sobre o quão bem o fluxo de trabalho é executado e como o tempo de execução dos aplicativos lógicos funciona de várias maneiras. Você pode monitorar seu fluxo de trabalho usando [Application insights](../azure-monitor/app/app-insights-overview.md), que fornece telemetria quase em tempo real (métricas ao vivo). Esse recurso pode ajudá-lo a investigar falhas e problemas de desempenho mais facilmente ao usar esses dados para diagnosticar problemas, configurar alertas e criar gráficos.

Se a criação e as configurações de implantação do aplicativo lógico dão suporte ao uso de [Application insights](../azure-monitor/app/app-insights-overview.md), você pode opcionalmente habilitar o log e o rastreamento de diagnóstico para seu aplicativo lógico. Isso pode ser feito quando você implanta seu aplicativo lógico do Visual Studio Code ou após a implantação. Você precisa ter uma instância de Application Insights, mas você pode criar esse recurso com [antecedência](../azure-monitor/app/create-workspace-resource.md), ao implantar seu aplicativo lógico ou após a implantação.

Para habilitar Application Insights em um aplicativo lógico implantado ou examinar Application Insights dados quando já estiver habilitado, siga estas etapas:

1. Na portal do Azure, localize seu aplicativo lógico implantado.

1. No menu do aplicativo lógico, em **configurações**, selecione **Application insights**.

1. Se Application Insights não estiver habilitado, no painel **Application insights** , selecione **Ativar Application insights**. Depois que o painel for atualizado, na parte inferior, selecione **aplicar**.

   Se Application Insights estiver habilitado, no painel **Application insights** , selecione **exibir dados de Application insights**.

Depois que Application Insights for aberto, você poderá examinar várias métricas para seu aplicativo lógico. Para obter mais informações, consulte estes tópicos:

* [Aplicativos lógicos do Azure em execução em qualquer lugar-monitor com Application Insights-parte 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Aplicativos lógicos do Azure em execução em qualquer lugar-monitor com Application Insights-parte 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Implantar no Docker

Você pode implantar seu aplicativo lógico em um [contêiner do Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) como o ambiente de hospedagem usando a [CLI do .net](/dotnet/core/tools/). Com esses comandos, você pode criar e publicar o projeto do aplicativo lógico. Em seguida, você pode compilar e executar o contêiner do Docker como o destino para implantar seu aplicativo lógico.

Se você não estiver familiarizado com o Docker, leia estes tópicos:

* [O que é o Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Introdução aos contêineres e ao Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introdução ao .NET e ao Docker](/dotnet/core/docker/introduction)
* [Registros, imagens e contêineres do Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Tutorial: introdução ao Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Requisitos

* A conta de armazenamento do Azure que seu aplicativo lógico usa para implantação

* Um arquivo do Docker para o fluxo de trabalho que você usa ao criar o contêiner do Docker

  Por exemplo, este arquivo do Docker de exemplo implanta um aplicativo lógico. O especifica a cadeia de conexão que contém a chave de acesso para a conta de armazenamento do Azure que foi usada para publicar o aplicativo lógico no portal do Azure. Para localizar essa cadeia de caracteres, consulte [obter cadeia de conexão da conta de armazenamento](#find-storage-account-connection-string).

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

   Para obter mais informações, consulte [práticas recomendadas para gravar arquivos do Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Obter cadeia de conexão da conta de armazenamento

Antes de criar e executar a imagem de contêiner do Docker, você precisa obter a cadeia de conexão que contém a chave de acesso para sua conta de armazenamento. Anteriormente, você criou essa conta de armazenamento como para usar a extensão no macOS ou Linux, ou quando implantou seu aplicativo lógico no portal do Azure.

Para localizar e copiar essa cadeia de conexão, siga estas etapas:

1. No portal do Azure, no menu da conta de armazenamento, em **configurações**, selecione **chaves de acesso**. 

1. No painel **chaves de acesso** , localize e copie a cadeia de conexão da conta de armazenamento, que é semelhante a este exemplo:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Captura de tela que mostra a portal do Azure com chaves de acesso da conta de armazenamento e cadeia de conexão copiadas.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Para obter mais informações, consulte [gerenciar chaves de conta de armazenamento](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Salve a cadeia de conexão em algum lugar seguro para que você possa adicionar essa cadeia de caracteres ao arquivo do Docker que você usa para a implantação. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Localizar chave mestra para conta de armazenamento

Quando o fluxo de trabalho contiver um gatilho de solicitação, você precisará [obter a URL de retorno de chamada do gatilho](#get-callback-url-request-trigger) depois de compilar e executar a imagem de contêiner do Docker. Para essa tarefa, você também precisa especificar o valor da chave mestra para a conta de armazenamento que você usa para a implantação.

1. Para localizar essa chave mestra, em seu projeto, abra o arquivo **Azure-webjobs-segredos/{nome-da-implantação}/host.js** .

1. Localize a `AzureWebJobsStorage` propriedade e copie o valor da chave desta seção:

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. Salve esse valor de chave em algum lugar seguro para que você use mais tarde.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Compilar e executar a imagem de contêiner do Docker

1. Crie a imagem de contêiner do Docker usando o arquivo do Docker e execute este comando:

   `docker build --tag local/workflowcontainer .`

   Para obter mais informações, consulte [Docker Build](https://docs.docker.com/engine/reference/commandline/build/).

1. Execute o contêiner localmente usando este comando:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Para obter mais informações, consulte [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Obter URL de retorno de chamada para o gatilho de solicitação

Para um fluxo de trabalho que usa o gatilho de solicitação, obtenha a URL de retorno de chamada do gatilho enviando esta solicitação:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

O `{trigger-name}` valor é o nome do gatilho de solicitação que aparece na definição de JSON do fluxo de trabalho. O `{master-key}` valor é definido na conta de armazenamento do Azure que você definiu para a `AzureWebJobsStorage` propriedade dentro do arquivo, **Azure-webjobs-segredos/{Deployment-name}/host.jsem**. Para obter mais informações, consulte [Localizar chave mestra da conta de armazenamento](#find-storage-account-master-key).

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Excluir itens do designer

Para excluir um item em seu fluxo de trabalho do designer, siga qualquer uma destas etapas:

* Selecione o item, abra o menu de atalho do item (Shift + F10) e selecione **excluir**. Para confirmar, selecione **OK**.

* Selecione o item e pressione a tecla Delete. Para confirmar, selecione **OK**.

* Selecione o item para que o painel de detalhes seja aberto para esse item. No canto superior direito do painel, abra o menu de reticências (**...**) e selecione **excluir**. Para confirmar, selecione **OK**.

  ![Captura de tela que mostra um item selecionado no designer com o painel de detalhes aberto mais o botão de reticências selecionadas e o comando "excluir".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Se o menu de reticências não estiver visível, expanda Visual Studio Code janela larga o suficiente para que o painel detalhes mostre o botão de reticências (**...**) no canto superior direito.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Solucionar problemas e erros

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Falha ao abrir o designer

Ao tentar abrir o designer, você recebe esse erro **"não foi possível iniciar o tempo de design do fluxo de trabalho"**. Se você já tentou abrir o designer e, em seguida, descontinuau ou excluiu seu projeto, o pacote de extensão pode não estar sendo baixado corretamente. Para verificar se essa causa é o problema, siga estas etapas:

  1. Em Visual Studio Code, abra a janela saída. No menu **Exibir** , selecione **saída**.

  1. Na lista da barra de título da janela de saída, selecione **aplicativos lógicos do Azure (versão prévia)** para que você possa examinar a saída da extensão, por exemplo:

     ![Captura de tela que mostra a janela de saída com "aplicativos lógicos do Azure" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Examine a saída e verifique se esta mensagem de erro é exibida:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Para resolver esse erro, exclua a pasta **ExtensionBundles** neste local **. ..\Users \{ Your-username} \AppData\Local\Temp\Functions\ExtensionBundles** e tente abrir novamente o **workflow.jsno** arquivo no designer.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Novos gatilhos e ações estão ausentes no seletor de designer para fluxos de trabalho criados anteriormente

A versão prévia dos aplicativos lógicos do Azure dá suporte a ações internas para operações de Azure Function, operações de Liquid e operações XML, como **validação de XML** e **XML de transformação**. No entanto, para aplicativos lógicos criados anteriormente, essas ações podem não aparecer no seletor de designer para que você selecione se Visual Studio Code usa uma versão desatualizada do pacote de extensão, `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Além disso, o conector de operações e as ações do **Azure function** não aparecem no seletor de designer, a menos que você tenha habilitado ou selecionado os **conectores de uso do Azure** quando você criou seu aplicativo lógico. Se você não habilitou os conectores implantados pelo Azure no momento da criação do aplicativo, poderá habilitá-los em seu projeto no Visual Studio Code. Abra o **workflow.jsno** menu de atalho e selecione **usar conectores do Azure**.

Para corrigir o pacote desatualizado, siga estas etapas para excluir o pacote desatualizado, que faz Visual Studio Code atualizar automaticamente o pacote de extensão para a versão mais recente.

> [!NOTE]
> Essa solução se aplica somente a aplicativos lógicos que você cria e implanta usando Visual Studio Code com a extensão de aplicativos lógicos do Azure (versão prévia), não os aplicativos lógicos que você criou usando o portal do Azure. Consulte [gatilhos e ações com suporte estão faltando no designer no portal do Azure](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Salve qualquer trabalho que você não deseja perder e feche o Visual Studio.

1. Em seu computador, navegue até a seguinte pasta, que contém pastas com controle de versão para o pacote existente:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Exclua a pasta versão do pacote anterior, por exemplo, se você tiver uma pasta para a versão 1.1.3, exclua essa pasta.

1. Agora, navegue até a seguinte pasta, que contém pastas com versão para o pacote NuGet necessário:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Exclua a pasta versão do pacote anterior, por exemplo, se você tiver uma pasta para a versão 1.0.0.8-Preview, exclua essa pasta.

1. Reabra Visual Studio Code, seu projeto e o **workflow.jsno** arquivo no designer.

Os gatilhos e as ações ausentes agora aparecem no designer.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 solicitação inadequada" aparece em um gatilho ou uma ação

Quando uma execução falha e você inspeciona a execução no modo de exibição monitoramento, esse erro pode aparecer em um gatilho ou ação que tem um nome mais longo, o que faz com que o URI (Uniform Resource Identifier) subjacente exceda o limite de caracteres padrão.

Para resolver esse problema e ajustar para o URI mais longo, edite as `UrlSegmentMaxCount` `UrlSegmentMaxLength` chaves do registro e no seu computador seguindo as etapas abaixo. Os valores padrão dessa chave são descritos neste tópico, [Http.sys configurações do registro para Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Antes de começar, certifique-se de salvar seu trabalho. Essa solução exige que você reinicie o computador depois de terminar para que as alterações entrem em vigor.

1. No computador, abra a janela **executar** e execute o `regedit` comando, que abre o editor do registro.

1. Na caixa **controle de conta de usuário** , selecione **Sim** para permitir suas alterações no computador.

1. No painel esquerdo, em **computador**, expanda os nós ao longo do caminho, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** e selecione **parâmetros**.

1. No painel direito, localize as `UrlSegmentMaxCount` chaves do `UrlSegmentMaxLength` registro e.

1. Aumente esses valores de chave o suficiente para que os URIs possam acomodar os nomes que você deseja usar. Se essas chaves não existirem, adicione-as à pasta **parâmetros** seguindo estas etapas:

   1. No menu de atalho **parâmetros** , selecione **novo**  >  **valor DWORD (32 bits)**.

   1. Na caixa de edição que aparece, insira `UrlSegmentMaxCount` como o novo nome de chave.

   1. Abra o menu de atalho da nova chave e selecione **Modificar**.

   1. Na caixa **Editar Cadeia de caracteres** exibida, insira o valor da chave de **dados do valor** desejado no formato hexadecimal ou Decimal. Por exemplo, `400` em hexadecimal é equivalente a `1024` em decimal.

   1. Para adicionar o `UrlSegmentMaxLength` valor da chave, repita essas etapas.

   Depois que você aumentar ou adicionar esses valores de chave, o editor do registro será semelhante a este exemplo:

   ![Captura de tela que mostra o editor do registro.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Quando estiver pronto, reinicie o computador para que as alterações entrem em vigor.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Falha na inicialização da sessão de depuração

Ao tentar iniciar uma sessão de depuração, você obtém o erro "o **erro existe depois de executar preLaunchTask ' generateDebugSymbols '"**. Para resolver esse problema, edite o **tasks.jsno** arquivo em seu projeto para ignorar a geração de símbolos.

1. Em seu projeto, expanda a pasta **. vscode** e abra o **tasks.jsno** arquivo.

1. Na tarefa a seguir, exclua a linha, `"dependsOn: "generateDebugSymbols"` , junto com a vírgula que termina a linha anterior, por exemplo:

   Antes:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Após:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Próximas etapas

Gostaríamos de conhecer suas experiências com a extensão de aplicativos lógicos do Azure (versão prévia)!

* Para bugs ou problemas, [crie seus problemas no GitHub](https://github.com/Azure/logicapps/issues).
* Para perguntas, solicitações, comentários e outros comentários, [Use este formulário de comentários](https://aka.ms/lafeedback).

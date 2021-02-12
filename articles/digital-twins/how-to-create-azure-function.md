---
title: Configurar uma função no Azure para processar dados
titleSuffix: Azure Digital Twins
description: Veja como criar uma função no Azure que pode acessar e ser disparada por gêmeos digital.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1178b0ab5af3642026fe78c7de788f354691b13a
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701146"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Conectar aplicativos de funções no Azure para processamento de dados

A atualização do gêmeos digital com base em dados é tratada usando [**rotas de eventos**](concepts-route-events.md) por meio de recursos de computação, como uma função feita usando [Azure Functions](../azure-functions/functions-overview.md). As funções podem ser usadas para atualizar uma atualização digital em resposta a:
* dados de telemetria do dispositivo provenientes do Hub IoT
* alteração de propriedade ou outros dados provenientes de outra troca digital no grafo de entrelaçamento

Este artigo orienta você pela criação de uma função no Azure para uso com o gêmeos digital do Azure. 

Aqui está uma visão geral das etapas que ela contém:

1. Criar um projeto do Azure Functions no Visual Studio
2. Escrever uma função com um gatilho de [grade de eventos](../event-grid/overview.md)
3. Adicione o código de autenticação à função (para poder acessar o Azure digital gêmeos)
4. Publicar o aplicativo de funções no Azure
5. Configurar o acesso de [segurança](concepts-security.md) para o aplicativo de funções

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Pré-requisito: configurar a instância de gêmeos digital do Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Criar um aplicativo de funções no Visual Studio

No Visual Studio 2019, selecione _arquivo > novo projeto de >_ e pesquise o modelo de _Azure Functions_ . Selecione _Avançar_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Captura de tela do Visual Studio mostrando a caixa de diálogo novo projeto. O modelo de projeto Azure Functions é realçado.":::

Especifique um nome para o aplicativo de funções e selecione _criar_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Captura de tela do Visual Studio que mostra a caixa de diálogo para configurar um novo projeto, incluindo nome do projeto, local para salvar, escolha para criar uma nova solução e nome da solução.":::

Selecione o tipo de aplicativo de função do *gatilho de grade de eventos* e selecione _criar_.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Captura de tela do Visual Studio que mostra a caixa de diálogo para criar um novo aplicativo Azure Functions. A opção de gatilho de grade de eventos está realçada.":::

Depois que seu aplicativo de funções for criado, o Visual Studio gerará um exemplo de código em um arquivo **function1.cs** na pasta do projeto. Essa função curta é usada para registrar eventos.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Captura de tela do Visual Studio na janela do projeto para o novo projeto que foi criado. Há código para uma função de exemplo chamada function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Escrever uma função com um gatilho de grade de eventos

Você pode escrever uma função adicionando o SDK ao seu aplicativo de funções. O aplicativo de funções interage com o gêmeos digital do Azure usando o [SDK do gêmeos digital do Azure para .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Para usar o SDK, você precisará incluir os pacotes a seguir em seu projeto. Você pode instalar os pacotes usando o Gerenciador de pacotes NuGet do Visual Studio ou adicionar os pacotes usando o `dotnet` em uma ferramenta de linha de comando. Siga as etapas abaixo para o seu método preferido.

**Opção 1. Adicionar pacotes usando o Gerenciador de pacotes do Visual Studio:**
    
Selecione o projeto com o botão direito do mouse e selecione _gerenciar pacotes NuGet_ na lista. Em seguida, na janela que é aberta, selecione a guia _procurar_ e procure os pacotes a seguir. Selecione _instalar_ e _aceite_ o contrato de licença para instalar os pacotes.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**Opção 2. Adicionar pacotes usando `dotnet` a ferramenta de linha de comando:**

Como alternativa, você pode usar os seguintes `dotnet add` comandos em uma ferramenta de linha de comando:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

Em seguida, no Gerenciador de Soluções do Visual Studio, abra o arquivo _function1.cs_ em que você tem um código de exemplo e adicione as instruções a seguir `using` à sua função. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Adicionar código de autenticação à função

Agora, você declarará variáveis de nível de classe e adicionará o código de autenticação que permitirá que a função acesse o Azure digital gêmeos. Você adicionará o seguinte à sua função no arquivo _function1.cs_ .

* Código para ler a URL do serviço gêmeos do Azure digital como uma variável de ambiente. É uma boa prática ler a URL do serviço de uma variável de ambiente, em vez de codificá-la embutidamente na função.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Uma variável estática para manter uma instância de HttpClient. O HttpClient é relativamente caro de criar e queremos evitar ter que fazer isso para cada invocação de função.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Você pode usar as credenciais de identidade gerenciadas no Azure Functions.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Adicione uma variável local _DigitalTwinsClient_ dentro de sua função para manter a instância do cliente gêmeos do Azure digital. Não *torne essa* variável estática dentro de sua classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Adicione uma verificação nula para _adtInstanceUrl_ e empacote sua lógica de função em um bloco try/catch para capturar quaisquer exceções.

Após essas alterações, o código de função será semelhante ao seguinte:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Agora que seu aplicativo foi escrito, você pode publicá-lo no Azure usando as etapas na próxima seção.

## <a name="publish-the-function-app-to-azure"></a>Publicar o aplicativo de funções no Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

## <a name="set-up-security-access-for-the-function-app"></a>Configurar o acesso de segurança para o aplicativo de funções

Você pode configurar o acesso de segurança para o aplicativo de funções usando o CLI do Azure ou o portal do Azure. Siga as etapas para sua opção preferida abaixo.

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Opção 1: configurar o acesso de segurança para o aplicativo de funções usando a CLI

O esqueleto de função dos exemplos anteriores requer que um token de portador seja passado para ele, a fim de ser capaz de autenticar com o gêmeos digital do Azure. Para garantir que esse token de portador seja passado, você precisará configurar [identidade de serviço gerenciada (MSI)](../active-directory/managed-identities-azure-resources/overview.md) para o aplicativo de funções. Isso só precisa ser feito uma vez para cada aplicativo de funções.

Você pode criar uma identidade gerenciada pelo sistema e atribuir a identidade do aplicativo de funções à função de _**proprietário de dados do gêmeos digital do Azure**_ para sua instância do gêmeos digital do Azure. Isso dará à permissão do aplicativo de funções na instância para executar atividades de plano de dados. Em seguida, torne a URL da instância do Azure digital gêmeos acessível para sua função definindo uma variável de ambiente.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Use [Azure cloud Shell](https://shell.azure.com) para executar os comandos.

Use o comando a seguir para criar a identidade gerenciada pelo sistema. Anote o campo _principalId_ na saída.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Use o valor de _principalId_ no comando a seguir para atribuir a identidade do aplicativo de funções à função de _Proprietário de Dados dos Gêmeos Digitais do Azure_ para sua instância dos Gêmeos Digitais do Azure.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Por fim, você pode tornar a URL da sua instância de gêmeos digital do Azure acessível para sua função definindo uma variável de ambiente. Para obter mais informações sobre como definir variáveis de ambiente, consulte [*variáveis de ambiente*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> A URL da instância do gêmeos digital do Azure é feita adicionando *https://* ao início do *nome de host* da instância do Azure digital gêmeos. Para ver o nome do host, juntamente com todas as propriedades de sua instância, você pode executar `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Opção 2: configurar o acesso de segurança para o aplicativo de funções usando portal do Azure

Uma identidade gerenciada atribuída pelo sistema permite que os recursos do Azure se autentiquem nos serviços de nuvem (por exemplo, Azure Key Vault) sem armazenar credenciais no código. Uma vez habilitada, todas as permissões necessárias podem ser concedidas por meio do controle de acesso baseado em função do Azure. O ciclo de vida desse tipo de identidade gerenciada está vinculado ao ciclo de vida deste recurso. Além disso, cada recurso (por exemplo, máquina virtual) pode ter apenas uma identidade gerenciada atribuída pelo sistema.

Na [portal do Azure](https://portal.azure.com/), pesquise por _aplicativo de funções_ na barra de pesquisa com o nome do aplicativo de funções que você criou anteriormente. Selecione o *aplicativo de funções* na lista. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Captura de tela da portal do Azure: o nome do aplicativo de funções está sendo pesquisado na barra de pesquisa do portal e o resultado da pesquisa é realçado.":::

Na janela do aplicativo de funções, selecione _identidade_ na barra de navegação à esquerda para habilitar a identidade gerenciada.
Na guia _atribuído pelo sistema_ , alterne o _status_ para ativado e _salve_ -o. Você verá um pop-up para _habilitar a identidade gerenciada atribuída pelo sistema_.
Selecione o botão _Sim_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Captura de tela da portal do Azure: na página identidade do aplicativo de funções, a opção para habilitar a identidade gerenciada atribuída pelo sistema está sendo definida como Sim. A opção status é definida como on.":::

Você pode verificar nas notificações de que sua função foi registrada com êxito com Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Captura de tela da portal do Azure: a lista de notificações selecionando o ícone em forma de sino na barra superior do Portal. Há uma notificação de que o usuário habilitou a identidade gerenciada atribuída pelo sistema.":::

Observe também a **ID de objeto** mostrada na página _identidade_ , pois ela será usada na próxima seção.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Captura de tela da portal do Azure: um realce ao contrário do campo ID do objeto da página identidade do Azure functions.":::

### <a name="assign-access-roles-using-azure-portal"></a>Atribuir funções de acesso usando portal do Azure

Selecione o botão _atribuições de função do Azure_ , que abrirá a página *atribuições de função do Azure* . Em seguida, selecione _+ Adicionar atribuição de função (versão prévia)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Captura de tela da portal do Azure: um realce ao contrário do botão atribuições de função do Azure em permissões na página de identidade da função do Azure.":::

Na página _Adicionar atribuição de função (versão prévia)_ que é aberta, selecione:

* _Escopo_: grupo de recursos
* _Assinatura_: selecione sua assinatura do Azure
* _Grupo de recursos_: selecione o grupo de recursos na lista suspensa
* _Função_: selecione _Azure digital gêmeos proprietário dos dados_ no menu suspenso

Em seguida, Salve seus detalhes pressionando o botão _salvar_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Captura de tela da caixa de diálogo portal do Azure: para adicionar uma nova atribuição de função (versão prévia). Há campos para o escopo, a assinatura, o grupo de recursos e a função.":::

### <a name="configure-application-settings-using-azure-portal"></a>Definir configurações de aplicativo usando portal do Azure

Você pode tornar a URL da sua instância do gêmeos digital do Azure acessível para sua função definindo uma variável de ambiente. Para obter mais informações sobre isso, consulte [*variáveis de ambiente*](/sandbox/functions-recipes/environment-variables). As configurações do aplicativo são expostas como variáveis de ambiente para acessar a instância digital gêmeos. 

Para definir uma variável de ambiente com a URL de sua instância, primeiro obtenha a URL encontrando o nome de host da instância do gêmeos digital do Azure. Pesquise sua instância na barra de pesquisa [portal do Azure](https://portal.azure.com) . Em seguida, selecione _visão geral_ na barra de navegação à esquerda para exibir o _nome do host_. Copie esse valor.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Captura de tela da portal do Azure: na página Visão geral da instância do gêmeos digital do Azure, o valor nome do host é realçado.":::

Agora você pode criar uma configuração de aplicativo seguindo as etapas abaixo:

1. Pesquise seu aplicativo de funções na barra de pesquisa do portal e selecione-o nos resultados
1. Selecione _configuração_ na barra de navegação à esquerda para criar uma nova configuração de aplicativo
1. Na guia _configurações do aplicativo_ , selecione _+ nova configuração de aplicativo_

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Captura de tela da portal do Azure: o nome do aplicativo de funções está sendo pesquisado na barra de pesquisa do portal e o resultado da pesquisa é realçado.":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Captura de tela da portal do Azure: na página de configuração do aplicativo de funções, o botão para criar uma nova configuração de aplicativo é realçado.":::

Na janela que é aberta, use o valor nome de host copiado acima para criar uma configuração de aplicativo.
* **Nome**: ADT_SERVICE_URL
* **Valor**: https://{Your-Azure-digital-gêmeos-Host-Name}

Selecione _OK_ para criar uma configuração de aplicativo.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Captura de tela da portal do Azure: o botão OK é realçado depois de preencher os campos de nome e valor na página Adicionar/Editar configuração de aplicativo.":::

Você pode exibir as configurações do aplicativo com o nome do aplicativo no campo _nome_ . Em seguida, salve as configurações do aplicativo selecionando o botão _salvar_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Captura de tela da portal do Azure: a página Configurações do aplicativo, com a nova configuração de ADT_SERVICE_URL realçada. O botão salvar também é realçado.":::

As alterações nas configurações do aplicativo exigirão que a reinicialização do aplicativo entre em vigor. Selecione _continuar_ para reiniciar o aplicativo.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Captura de tela da portal do Azure: há um aviso de que há alterações nas configurações do aplicativo com a reinicialização do aplicativo. O botão continuar é realçado.":::

Você pode exibir que as configurações do aplicativo são atualizadas selecionando o ícone de _notificações_ . Se a configuração do aplicativo não for criada, você poderá repetir a adição de uma configuração de aplicativo seguindo o processo acima.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Captura de tela da portal do Azure: a lista de notificações selecionando o ícone em forma de sino na barra superior do Portal. Há uma notificação de que as configurações do aplicativo Web foram atualizadas com êxito.":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você seguiu as etapas para configurar um aplicativo de funções no Azure para uso com o gêmeos digital do Azure.

Em seguida, consulte como criar sua função básica para ingerir dados do Hub IoT no gêmeos digital do Azure:
* [*Como: telemetria de ingestão do Hub IoT*](how-to-ingest-iot-hub-data.md)

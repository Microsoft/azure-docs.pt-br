---
title: Configurar uma instância e uma autenticação (com script)
titleSuffix: Azure Digital Twins
description: Consulte como configurar uma instância do serviço de gêmeos digital do Azure, executando um script de implantação automatizada
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1552401953a8cba9dda787a0f0e461adb7972920
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664445"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (com script)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo conclui essas etapas executando um exemplo de [ **script de implantação automatizado**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) que simplifica o processo. 
* Para exibir as etapas manuais da CLI em que o script é executado nos bastidores, consulte a versão da CLI deste artigo: [*como: configurar uma instância e autenticação (CLI)*](how-to-set-up-instance-cli.md).
* Para exibir as etapas manuais de acordo com o portal do Azure, consulte a versão do portal deste artigo: [*como configurar uma instância e autenticação (Portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Pré-requisitos: baixar o script

O script de exemplo é escrito no PowerShell. Ele faz parte dos [**exemplos de ponta a ponta do Azure digital gêmeos**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), que você pode baixar em seu computador navegando até esse link de exemplo e selecionando o botão *Procurar código* abaixo do título. Isso o levará para o repositório do GitHub para os exemplos que você pode baixar como um *. ZIP* selecionando o botão de *código* e *baixando o zip*.

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="Exibição do repositório digital-gêmeos-Samples no GitHub. O botão código é selecionado, produzindo uma pequena caixa de diálogo na qual o botão baixar ZIP é realçado." lightbox="media/includes/download-repo-zip.png":::

Isso fará o download de um *. Pasta ZIP* em seu computador como **digital-twins-samples-master.zip**. Navegue até a pasta em seu computador e descompacte-a para extrair os arquivos.

Na pasta descompactada, o script de implantação está localizado em _digital-gêmeos-Samples-master > scripts > **deploy.ps1**_.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Executar o script de implantação

Este artigo usa um exemplo de código de gêmeos digital do Azure para implantar uma instância do gêmeos digital do Azure e a autenticação necessária semi-automaticamente. Ele também pode ser usado como um ponto de partida para escrever suas próprias interações com script.

Aqui estão as etapas para executar o script de implantação no Cloud Shell.
1. Vá para uma janela de [Azure cloud Shell](https://shell.azure.com/) em seu navegador. Entre usando este comando:
    ```azurecli-interactive
    az login
    ```
    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure. Caso contrário, abra uma página de navegador em *https://aka.ms/devicelogin* e insira o código de autorização exibido no terminal.
 
2. Na barra de ícones Cloud Shell, verifique se o Cloud Shell está configurado para executar a versão do PowerShell.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Janela Cloud Shell mostrando a seleção da versão do PowerShell":::

1. Selecione o ícone "carregar/baixar arquivos" e escolha "carregar".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Janela Cloud Shell mostrando a seleção do ícone carregar":::

    Navegue até o arquivo de _**deploy.ps1**_ em seu computador (em _digital-gêmeos-Samples-master > scripts > **deploy.ps1**_) e pressione "abrir". Isso carregará o arquivo para Cloud Shell para que você possa executá-lo na janela Cloud Shell.

4. Execute o script enviando o `./deploy.ps1` comando na janela Cloud Shell. Você pode copiar o comando abaixo (Lembre-se de que para colar em Cloud Shell, você pode usar **Ctrl + Shift + v** no Windows e Linux ou **cmd + shift + v** no MacOS. Você também pode usar o menu do botão direito do mouse.

    ```azurecli-interactive
    ./deploy.ps1
    ```

    O script criará uma instância de gêmeos digital do Azure e atribuirá ao usuário do Azure a função de *proprietário de dados do gêmeos digital do Azure* na instância.

    À medida que o script é executado por meio das etapas de configuração automatizada, você será solicitado a passar os seguintes valores:
    * Para a instância: a *ID* da assinatura do Azure a ser usada
    * Para a instância: um *local* onde você gostaria de implantar a instância. Para ver quais regiões dão suporte ao Azure digital gêmeos, visite [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Para a instância: um nome de *grupo de recursos* . Você pode usar um grupo de recursos existente ou inserir um novo nome de um para criar.
    * Para a instância: um *nome* para sua instância de gêmeos digital do Azure. Se sua assinatura tiver outra instância de gêmeos digital do Azure na região que já está usando o nome especificado, você será solicitado a escolher um nome diferente.

Aqui está um trecho do log de saída do script:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Janela Cloud Shell mostrando o log de entrada e saída por meio da execução do script de implantação" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Se o script for concluído com êxito, a impressão final dirá `Deployment completed successfully` . Caso contrário, resolva a mensagem de erro e execute o script novamente. Ele ignorará as etapas que você já concluiu e começará a solicitar a entrada novamente no ponto em que você parou.

> [!NOTE]
> O script atribui atualmente a função de gerenciamento necessária no Azure digital gêmeos (*proprietário de dados do Azure digital gêmeos*) ao mesmo usuário que executa o script de Cloud Shell. Se você precisar atribuir essa função a outra pessoa que gerenciará a instância, poderá fazer isso agora por meio do portal do Azure ([instruções](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) ou da CLI ([instruções](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

>[!NOTE]
>Atualmente, há um **problema conhecido** com a instalação com script, na qual alguns usuários (especialmente usuários sobre [contas pessoais da Microsoft (MSAS)](https://account.microsoft.com/account)) podem achar que a **atribuição de função ao _proprietário de dados do Azure digital gêmeos_ não foi criada**.
>
>Você pode verificar a atribuição de função com a seção [*verificar atribuição de função de usuário*](#verify-user-role-assignment) posteriormente neste artigo e, se necessário, configurar a atribuição de função manualmente usando o [portal do Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions) ou a [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Para obter mais detalhes sobre esse problema, confira [*Solução de problemas: Problemas conhecidos nos Gêmeos Digitais do Azure*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## <a name="verify-success-and-collect-important-values"></a>Verificar o êxito e coletar valores importantes

Para verificar a criação de seus recursos e as permissões configuradas pelo script, você pode vê-los no [portal do Azure](https://portal.azure.com) com as instruções abaixo. Se não for possível verificar o êxito de qualquer etapa, repita a etapa. Você pode executar as etapas individualmente usando as instruções [portal do Azure](how-to-set-up-instance-portal.md) ou [CLI](how-to-set-up-instance-cli.md) .

Esta seção também mostra como localizar valores importantes dos recursos configurados pelo script que você pode precisar ao continuar trabalhando com sua instância do gêmeos digital do Azure. Você deve salvar esses valores em um local seguro ou retornar a esta seção para encontrá-los mais tarde quando precisar deles. Se outros usuários estiverem programando em relação à instância, você também deverá compartilhar esses valores com eles.

### <a name="verify-instance"></a>Verificar instância

Para verificar se a instância foi criada, vá para a [página gêmeos digital do Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) na portal do Azure. Você pode acessar esta página por conta própria procurando por *gêmeos do Azure digital* na barra de pesquisa do Portal.

Esta página lista todas as suas instâncias de gêmeos digitais do Azure. Procure o nome da instância recém-criada na lista.

Se a verificação não tiver sido bem-sucedida, você poderá repetir a criação de uma instância usando o [portal](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) ou a [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="collect-instance-values"></a>Coletar valores de instância

Selecione o nome da instância na [página gêmeos do Azure digital](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) para abrir a página *visão geral* da instância. Anote seu *nome*, *grupo de recursos* e *nome do host*. Talvez você precise deles mais tarde para identificar e conectar-se à sua instância do.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Realçando os valores importantes da página de visão geral da instância":::

### <a name="verify-user-role-assignment"></a>Verificar atribuição de função de usuário

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Lembre-se de que o script atribui atualmente essa função necessária ao mesmo usuário que executa o script de Cloud Shell. Se você precisar atribuir essa função a outra pessoa que gerenciará a instância, poderá fazer isso agora por meio do portal do Azure ([instruções](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) ou da CLI ([instruções](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Se a verificação não tiver sido bem-sucedida, você também poderá refazer sua própria atribuição de função usando o [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) ou a [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

## <a name="next-steps"></a>Próximas etapas

Teste as chamadas de API REST individuais em sua instância usando os comandos da CLI do Azure digital gêmeos: 
* [referência de AZ DT](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Como usar a CLI dos Gêmeos Digitais do Azure*](how-to-use-cli.md)

Ou então, consulte Como conectar um aplicativo cliente à sua instância com o código de autenticação:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)
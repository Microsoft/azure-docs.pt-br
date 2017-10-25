---
title: "CI/CD com Serviço de Contêiner do Azure e Swarm | Microsoft Docs"
description: "Usar o Serviço de Contêiner do Azure com o Docker Swarm, um Registro de Contêiner do Azure e o Visual Studio Team Services para fornecer continuamente um aplicativo .NET Core com vários contêineres"
services: container-service
documentationcenter: " "
author: jcorioland
manager: pierlag
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Swarm, Azure, Visual Studio Team Services, DevOps"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 99c27c37218a35d2a3416d6edd5e0a871cd5c011
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Pipeline de CI/CD completo para implantar um aplicativo com vários contêineres no Serviço de Contêiner do Azure com Docker Swarm usando o Visual Studio Team Services

Um dos maiores desafios ao desenvolver aplicativos modernos para a nuvem é ser capaz de fornecer esses aplicativos continuamente. Neste artigo, você aprenderá a implementar uma integração contínua completa e o pipeline de implantação (IC/CD) usando o Serviço de Contêiner do Azure com Docker Swarm, Registro de Contêiner do Azure, compilação do Visual Studio Team Services e gerenciamento da versão.

Este artigo se baseia em um aplicativo simples, disponível no [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), desenvolvido com o ASP.NET Core. O aplicativo é composto de quatro serviços diferentes: três APIs da Web e um front-end da Web:

![Aplicativo de exemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

O objetivo é fornecer esse aplicativo continuamente em um cluster Docker Swarm usando o Visual Studio Team Services. A figura a seguir fornece detalhes sobre esse pipeline de entrega contínua:

![Aplicativo de exemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Uma breve explicação das etapas:

1. As alterações do código são confirmadas no repositório do código-fonte (aqui, GitHub) 
2. O GitHub dispara uma compilação no Visual Studio Team Services 
3. O Visual Studio Team Services obtém a versão mais recente das fontes e compila todas as imagens que compõem o aplicativo 
4. O Visual Studio Team Services envia cada imagem para um registro do Docker criado usando o serviço do Registro de Contêiner do Azure 
5. O Visual Studio Team Services dispara uma nova versão 
6. A versão executa alguns comandos usando o SSH no nó mestre do cluster do serviço de contêiner do Azure 
7. O Docker Swarm no cluster obtém a versão mais recente das imagens 
8. A nova versão do aplicativo é implantada usando o Docker Compose 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você precisa concluir as seguintes tarefas:

- [Criar um Cluster Swarm no Serviço de Contêiner do Azure](container-service-deployment.md)
- [Conectar-se ao cluster Swarm no Serviço de Contêiner do Azure](../container-service-connect.md)
- [Criar um registro de contêiner do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Ter uma conta do Visual Studio Team Services e projeto de equipe criados](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Dividir o repositório GitHub para sua conta do GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Você também precisa de um computador Ubuntu (14.04 ou 16.04) com o Docker instalado. Esta máquina é usada pelo Visual Studio Team Services durante os processos de compilação e versão. Uma maneira de criar essa máquina é usar a imagem disponível no [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Etapa 1: Configurar sua conta do Visual Studio Team Services 

Nesta seção, você configura sua conta do Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Configurar um agente de compilação Linux do Visual Studio Team Services

Para criar imagens do Docker e enviar essas imagens para um registro de contêiner do Azure a partir de uma compilação do Visual Studio Team Services, você precisa registrar um agente Linux. Você tem estas opções de instalação:

* [Implantar um agente no Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Usar o Docker para executar o agente VSTS](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Instalar a extensão VSTS da Integração com o Docker

A Microsoft fornece uma extensão VSTS para trabalhar com o Docker nos processos de compilação e versão. Essa extensão está disponível no [VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Clique em **instalar** para adicionar essa extensão à sua conta VSTS:

![Instalar a Integração com o Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Você será solicitado a conectar sua conta VSTS usando suas credenciais. 

### <a name="connect-visual-studio-team-services-and-github"></a>Conectar o Visual Studio Team Services e o GitHub

Configure uma conexão entre seu projeto VSTS e sua conta GitHub.

1. Em seu projeto do Visual Studio Team Services, clique no ícone **Configurações** na barra de ferramentas e selecione **Serviços**.

    ![Visual Studio Team Services - Conexão Externa](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. À esquerda, clique em **Novo Ponto de Extremidade de Serviço** > **GitHub**.

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. Para autorizar o VSTS a trabalhar com sua conta GitHub, clique em **Autorizar** e siga o procedimento na janela aberta.

    ![Visual Studio Team Services - Autorizar GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Conectar o VSTS ao registro de contêiner do Azure e ao cluster Serviço de Contêiner do Azure

As últimas etapas antes de entrar no pipeline de CI/CD são para configurar as conexões externas com seu registro do contêiner e o cluster Docker Swarm no Azure. 

1. Nas configurações **Serviços** do seu projeto do Visual Studio Team Services, adicione um ponto de extremidade de serviço do tipo **Registro do Docker**. 

2. No popup aberto, digite a URL e as credenciais do registro de contêiner do Azure.

    ![Visual Studio Team Services - Registro do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Para o cluster Docker Swarm, adicione um ponto de extremidade do tipo **SSH**. Então, insira as informações de conexão SSH do seu cluster Swarm.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toda a configuração é concluída agora. As próximas etapas, você criará o pipeline de CI/CD que compila e implanta o aplicativo no cluster Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Etapa 2: Criar a definição da compilação

Nesta etapa, você configura uma definição da compilação para seu projeto VSTS e define o fluxo de trabalho da compilação para suas imagens de contêiner

### <a name="initial-definition-setup"></a>Configuração da definição inicial

1. Para criar uma definição da compilação, conecte seu projeto do Visual Studio Team Services e clique em **Compilação e Versão**. 

2. Na seção **Definições da compilação**, clique em **+ Novo**. Selecione o modelo **Vazio**.

    ![Visual Studio Team Services - Nova Definição da Compilação](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Configure a nova compilação com uma fonte de repositório GitHub, marque **Integração contínua** e selecione a fila do agente na qual você registrou seu agente Linux. Clique em **Criar** para criar a definição da compilação.

    ![Visual Studio Team Services - Criar Definição da Compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. Na página **Definições da Compilação**, primeiro abra a guia **Repositório** e configure a compilação para usar a bifurcação do projeto MyShop que você criou nos pré-requisitos. Verifique se você selecionou *acs-docs* como o **Ramificação padrão**.

    ![Visual Studio Team Services - Configuração do Repositório de Compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. Na guia **Gatilhos**, configure a compilação a ser disparada após cada confirmação. Selecione **Integração contínua** e **Alterações de lote**.

    ![Visual Studio Team Services - Configuração do Gatilho de Compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definir o fluxo de trabalho da compilação
As próximas etapas definem o fluxo de trabalho da compilação. Há cinco imagens de contêiner a compilar para o aplicativo *MyShop*. Cada imagem é compilada usando o arquivo Docker localizado nas pastas do projeto:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Você precisa adicionar duas etapas do Docker para cada imagem, uma para compilar a imagem e outra para enviar a imagem no registro de contêiner do Azure. 

1. Para adicionar uma etapa no fluxo de trabalho da compilação, clique em **+ Adicionar etapa de compilação** e selecione **Docker**.

    ![Visual Studio Team Services - Adicionar Etapas da Compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Para cada imagem, configure uma etapa que usa o comando `docker build`.

    ![Visual Studio Team Services - Compilação do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Para a operação de compilação, selecione o registro de contêiner do Azure, a ação **Criar uma imagem** e o arquivo Docker que define cada imagem. Defina o **Contexto da compilação** como o diretório-raiz do arquivo Docker e defina o **Nome da Imagem**. 
    
    Como mostrado na tela anterior, inicie o nome da imagem com o URI do registro de contêiner do Azure. (Você também pode usar uma variável de compilação para parametrizar a marcação da imagem, como o identificador da compilação neste exemplo.)

3. Para cada imagem, configure uma segunda etapa que usa o comando `docker push`.

    ![Visual Studio Team Services - Envio do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Para a operação de envio, selecione o registro de contêiner do Azure, a ação **Enviar uma imagem** e insira o **Nome da Imagem** compilado na etapa anterior.

4. Depois de configurar as etapas de compilação e envio para cada uma das cinco imagens, adicione mais duas etapas do fluxo de trabalho de compilação.

    a. Uma tarefa da linha de comando que usa um script bash para substituir a ocorrência *BuildNumber* no arquivo docker-compose.yml pela ID atual de compilação. Consulte a tela a seguir para obter detalhes.

    ![Visual Studio Team Services - Atualizar arquivo de composição](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Uma tarefa que cancela o arquivo de Composição atualizado como um artefato de compilação para que ele possa ser usado na versão. Consulte a tela a seguir para obter detalhes.

    ![Visual Studio Team Services - Publicar arquivo de composição](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Clique em **Salvar** e nomeie sua definição de compilação.

## <a name="step-3-create-the-release-definition"></a>Etapa 3: Criar a definição da versão

O Visual Studio Team Services permite que você [gerencie as versões nos ambientes](https://www.visualstudio.com/team-services/release-management/). Você pode habilitar uma implantação contínua para verificar se seu aplicativo é implantado em seus ambientes diferentes (como de desenvolvimento, teste, pré-produção e produção) de forma suave. Você pode criar um novo ambiente que representa o cluster Docker Swarm do Serviço de Contêiner do Azure.

![Visual Studio Team Services - Versão para o ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração da versão inicial

1. Para criar uma definição da versão, clique em **Versões** > **+ Versão**

2. Para configurar a fonte do artefato, clique em **Artefatos** > **Vincular uma fonte do artefato**. Aqui, vincule essa nova definição da versão à compilação definida na etapa anterior. Fazendo isso, o arquivo docker-compose.yml fica disponível no processo da versão.

    ![Visual Studio Team Services - Artefatos da Versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. Para configurar o gatilho da versão, clique em **Gatilhos** e selecione **Implantação Contínua**. Defina o gatilho na mesma fonte do artefato. Essa configuração garante que uma nova versão comece assim que a compilação é concluída com êxito.

    ![Visual Studio Team Services - Gatilhos da Versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definir o fluxo de trabalho da versão

A versão do fluxo de trabalho é composta de duas tarefas que você adiciona.

1. Configure uma tarefa para copiar com segurança o arquivo de composição para uma pasta de *implantação* no nó mestre Docker Swarm usando a conexão SSH configurada anteriormente. Consulte a tela a seguir para obter detalhes.

    ![Visual Studio Team Services - SCP da Versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Configure uma segunda tarefa para executar um comando bash e os comandos `docker` e `docker-compose` no nó mestre. Consulte a tela a seguir para obter detalhes.

    ![Visual Studio Team Services - Bash da Versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    O comando executado no mestre usa a CLI do Docker e a CLI do Docker Compose para realizar as seguintes tarefas:

    - Logon no registro de contêiner do Azure (usa três variáveis de compilação definidas na guia **Variáveis**)
    - Definir a variável **DOCKER_HOST** para trabalhar com o ponto de extremidade Swarm (:2375)
    - Navegue até a pasta de *implantação* criada pela tarefa da cópia de segurança anterior e que contém o arquivo docker-compose.yml 
    - Execute os comandos `docker-compose` que recebem as novas imagens, interrompem e removem os serviços, e criam os contêineres.

    >[!IMPORTANT]
    > Como mostrado na tela anterior, deixe a caixa de seleção **Falha no STDERR** desmarcada. Esta é uma configuração importante, porque `docker-compose` imprime várias mensagens de diagnóstico, como os contêineres estão parando ou sendo excluídos, na saída de erro padrão. Se você marcar a caixa de seleção, o Visual Studio Team Services informará que ocorreram erros durante a versão, mesmo que tudo tenha corrido bem.
    >
3. Salve a nova definição da versão.


>[!NOTE]
>Essa implantação inclui um tempo de inatividade porque estamos interrompendo os antigos serviços e executando novos. É possível evitar isso fazendo uma implantação com redução da inatividade.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Etapa 4. Testar o pipeline de CI/CD

Agora que você concluiu a configuração, é hora de testar esse novo pipeline de CI/CD. A maneira mais fácil de testá-lo é atualizar o código-fonte e confirmar as alterações no repositório GitHub. Alguns segundos depois de enviar o código, você verá uma nova compilação em execução no Visual Studio Team Services. Depois de concluído com êxito, uma nova versão será disparada e implantará a nova versão do aplicativo no cluster Serviço de Contêiner do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a CI/CD com o Visual Studio Team Services, consulte a [Visão geral da Compilação do VSTS](https://www.visualstudio.com/docs/build/overview).

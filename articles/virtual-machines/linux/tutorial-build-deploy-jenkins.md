---
title: CI/CD do Jenkins para VMs do Azure com o Team Services | Microsoft Docs
description: "Configurar a CI (integração contínua) e a CD (implantação contínua) de um aplicativo Node.js usando Jenkins para VMs do Azure do Release Management no VSTS (Visual Studio Team Services) ou Microsoft TFS (Team Foundation Server)"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: feaced0d0784b5724fb1e30be5e66cb7c808d77f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>Implantar seu aplicativo em VMs Linux usando Jenkins e Team Services

CI (integração contínua) e CD (implantação contínua) é um pipeline por meio do qual você pode compilar, lançar e implantar seu código. O Team Services fornece um conjunto completo de ferramentas de automação de CI/CD para implantação no Azure. Jenkins é uma ferramenta de terceiros popular baseada em servidor de CI/CD que também fornece a automação de CI/CD. Você pode usar os dois juntos para personalizar o fornecimento de seu aplicativo ou serviço de nuvem.

Neste tutorial, você usará o Jenkins para criar um **aplicativo Web Node.js** e o Visual Studio Team Services para implantá-lo em um [grupo de implantação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) que contém máquinas virtuais Linux.

Você vai:

> [!div class="checklist"]
> * Compilar seu aplicativo no Jenkins
> * Configurar o Jenkins para integração com o Team Services
> * Criar um grupo de implantação para máquinas virtuais do Azure
> * Criar uma definição de versão que configura as VMs e implanta o aplicativo

## <a name="before-you-begin"></a>Antes de começar

* Você precisa ter acesso a uma conta do Jenkins. Se você ainda não tiver criado um servidor Jenkins, confira a [Documentação do Jenkins](https://jenkins.io/doc/). 

* Entre em sua conta do Team Services (`https://{youraccount}.visualstudio.com`). 
  Você pode obter uma [conta gratuita do Team Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para saber mais, confira [Conectar-se ao Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

* Crie um PAT (token de acesso pessoal) em sua conta do Team Services se você ainda não tiver um. O Jenkins exige essas informações para acessar sua conta do Team Services.
  Leia [Como criar um token de acesso pessoal para o Team Services e o TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) para aprender a gerar um.

## <a name="get-the-sample-app"></a>Obter o aplicativo de exemplo

Você precisa de um aplicativo para implantação em um repositório Git.
Para este tutorial, recomendamos o uso [deste aplicativo de exemplo disponível no GitHub](https://github.com/azooinmyluggage/fabrikam-node).

1. Crie uma bifurcação deste aplicativo e anote o local (URL) para usar em etapas posteriores deste tutorial.

1. Torne a bifurcação **pública** para simplificar a conexão posterior ao GitHub.

> [!NOTE]
> Para saber mais, confira [Criar bifurcação de um repositório](https://help.github.com/articles/fork-a-repo/) e [Transformar em público um repositório privado](https://help.github.com/articles/making-a-private-repository-public/).

> [!NOTE]
> O aplicativo foi compilado usando [Yeoman](http://yeoman.io/learning/index.html); ele usa **Express**, **bower** e **grunt**; e tem alguns pacotes **npm** como dependências.
> O aplicativo de exemplo contém um conjunto de [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) que são usados para criar dinamicamente as máquinas virtuais para implantação no Azure. Esses modelos são usados por tarefas na [definição de versão do Team Services](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions).
> O modelo principal cria um grupo de segurança de rede, uma máquina virtual e uma rede virtual. Ele atribui um endereço IP público e abre a porta 80 de entrada. Ele também adiciona uma marca usada pelo grupo de implantação para selecionar as máquinas que receberão a implantação.
>
> O exemplo também contém um script que configura o Nginx e implanta o aplicativo. Ele é executado em cada uma das máquinas virtuais. Especificamente, o script instala o Node, o Nginx e o PM2; configura Nginx e PM2; depois, inicia o aplicativo Node.

## <a name="configure-jenkins-plugins"></a>Configurar os plug-ins do Jenkins

Primeiro, você deve configurar dois plug-ins Jenkins para **NodeJS** e **Integração com o Team Services**.

1. Abra sua conta do Jenkins e escolha **Gerenciar Jenkins**.

1. Na página **Gerenciar Jenkins**, escolha **Gerenciar Plug-ins**.

1. Filtre a lista para localizar o plug-in **NodeJS** e instalá-lo sem reinicialização.

   ![Como adicionar o plug-in do NodeJS ao Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. Filtre a lista para localizar o plug-in **Team Foundation Server** e instalá-lo. (Esse plug-in funciona para Team Services e Team Foundation Server.) Não é necessário reiniciar o Jenkins.

## <a name="configure-jenkins-build-for-nodejs"></a>Configurar o build do Jenkins para Node.js

No Jenkins, crie um novo projeto de build e configure-o da seguinte maneira:

1. Na guia **Geral**, insira um nome para seu projeto de build.

1. Na guia **Gerenciamento de Código-Fonte**, selecione **Git** e insira os detalhes do repositório e do branch que contém o código do aplicativo.

   ![Adicionar um repositório ao seu build](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > Se o repositório não for público, escolha **Adicionar** e forneça as credenciais para se conectar a ele.

1. Na guia **Compilar Gatilhos**, selecione **Sondar SCM** e insira o cronograma `H/03 * * * *` para sondar o repositório do Git em busca de alterações a cada três minutos. 

1. Na guia **Ambiente de Build**, selecione **Fornecer Nó &amp; CAMINHO do compartimento/pasta de npm** e insira `NodeJS` para o valor de instalação do Node JS. Deixe o **arquivo npmrc** definido como "usar padrão do sistema".

1. Na guia **Build**, insira o comando `npm install` para garantir a atualização de todas as dependências.

## <a name="configure-jenkins-for-team-services-integration"></a>Configurar o Jenkins para integração com o Team Services

1. Na guia **Ações Pós-build**, em **Arquivos para arquivar**, insira `**/*` para incluir todos os arquivos.

1. Para **Disparar versão no TFS/Team Services**, insira a URL completa de sua conta (como `https://your-account-name.visualstudio.com`), o nome do projeto, um nome para a definição de versão (criado posteriormente) e as credenciais para conectar-se à sua conta.
   Você precisa de seu nome de usuário e do PAT criado anteriormente. 

   ![Configuração de ações pós-build do Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. Salve o projeto de build.

## <a name="create-a-jenkins-service-endpoint"></a>Criar um ponto de extremidade de serviço do Jenkins

Um ponto de extremidade de serviço permite que o Team Services se conecte ao Jenkins.

1. Abra a página **Serviços** no Team Services, abra a lista **Novo Ponto de Extremidade de Serviço** e escolha **Jenkins**.

   ![Adicionar um ponto de extremidade do Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. Insira um nome que você usará para se referir a essa conexão.

1. Insira a URL de seu servidor Jenkins e marque a opção **Aceitar certificados SSL não confiáveis**.

1. Insira o nome de usuário e a senha para sua conta do Jenkins.

1. Escolha **Verificar conexão** para verificar se as informações estão corretas.

1. Escolha **OK** para criar o ponto de extremidade de serviço.

## <a name="create-a-deployment-group"></a>Criar um grupo de implantação

Você precisa de um [grupo de implantação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para conter as máquinas virtuais.

1. Abra a guia **Versões** do hub **Build &amp; Versão**, abra a guia **Grupos de implantação** e escolha **+ Novo**.

1. Insira um nome para o grupo de implantação e uma descrição opcional.
   Depois, escolha **Criar**.

A tarefa Implantação do Grupo de Recursos do Azure cria e registra as VMs quando é executada usando o modelo do Azure Resource Manager.
Não é necessário criar e registrar as máquinas virtuais por conta própria.

## <a name="create-a-release-definition"></a>Criar uma definição de versão

Uma definição de versão especifica o processo que o Team Services executará para implantar o aplicativo.
Para criar uma definição de versão no Team Services:

1. Abra a guia **Versões** do hub **Build &amp; Versão**, abra a lista suspensa **+** na lista de definições de versão e escolha **Criar definição de versão**. 

1. Selecione o modelo **Vazio** e depois **Avançar**.

1. Na seção **Artefatos**, clique em **Vincular um Artefato** e escolha **Jenkins**. Selecione sua conexão de ponto de extremidade de serviço do Jenkins. Depois, selecione o trabalho de origem do Jenkins e escolha **Criar**. 

1. Na definição de nova versão, escolha **+ Adicionar tarefas** e adicione uma tarefa **Implantação de Grupo de Recursos do Azure** para o ambiente padrão.

1. Escolha a seta suspensa ao lado do link **+ Adicionar tarefas** e adicione uma fase de grupo de implantação à definição.

   ![Adicionar uma fase de grupo de implantação](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. No catálogo de Tarefas, abra a seção **Utilitário** e adicione uma instância da tarefa **Script do Shell**.

1. O modelo de parâmetros usado na tarefa Implantação de Grupo de Recursos do Azure define a senha de administrador usada para se conectar às VMs.
   Forneça essa senha com a variável **$(adminpassword)**:
   
   - Abra a guia **Variáveis** e, na seção **Variáveis**, insira o nome `adminpassword`.

   - Insira a senha de administrador.

   - Escolha o ícone "cadeado" ao lado da caixa de texto de valor para proteger a senha. 

## <a name="configure-the-azure-resource-group-deployment-task"></a>Configurar a tarefa Implantação do Grupo de Recursos do Azure

A tarefa **Implantação do Grupo de Recursos do Azure** é usada para criar o grupo de implantação. Configure-o da seguinte maneira:

* **Assinatura do Azure:** selecione uma conexão na lista em **Conexões Disponíveis do Serviço do Azure**. 
  Se nenhuma conexão aparecer, escolha **Gerenciar**, selecione **Novo Ponto de Extremidade de Serviço**, **Azure Resource Manager** e siga os prompts.
  Volte à sua definição de versão, atualize a lista **Assinatura do AzureRM** e selecione a conexão que você criou.

* **Grupo de recursos**: insira um nome do grupo de recursos que você criou anteriormente.

* **Local**: selecione uma região para a implantação.

  ![Criar um novo grupo de recursos](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **Local do modelo**: `URL of the file`

* **Link do modelo**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.json`

* **Link de parâmetros de modelo**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`

* **Substituir parâmetros do modelo**: uma lista com os valores de substituição, por exemplo: `-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`.<br />Insira seus próprios valores específicos para {espaços reservados}. 

* **Habilitar os pré-requisitos**: `Configure with Deployment Group agent`

* **Ponto de extremidade TFS/VSTS**: escolha **Adicionar** e, na caixa de diálogo "Adicionar nova Conexão do Team Foundation Server/Team Services", selecione **Autenticação com Base em Token**. Insira um nome da conexão e a URL do projeto de sua equipe. Em seguida, gere e insira um [PAT (Token de Acesso Pessoal)]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) para autenticar a conexão com o projeto de sua equipe.

  ![Criar um token de acesso pessoal](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **Projeto de equipe**: selecione o projeto atual.

* **Grupo de Implantação**: insira o mesmo nome de grupo de implantação que você usou para o parâmetro **Grupo de recursos**.

As configurações padrão para a tarefa de Implantação do Grupo de Recursos do Azure são criar ou atualizar um recurso, e fazer isso de forma incremental. A tarefa cria as VMs na primeira vez que é executada e, depois, apenas as atualiza.

## <a name="configure-the-shell-script-task"></a>Configurar a tarefa de Script do Shell

A tarefa **Script do Shell** é usada para fornecer a configuração para execução de um script em cada servidor a fim de instalar o Node.js e iniciar o aplicativo. Configure-o da seguinte maneira:

* **Caminho do Script**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`

* **Especificar Diretório de Trabalho**: `Checked`

* **Diretório de Trabalho**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
   
## <a name="rename-and-save-the-release-definition"></a>Renomear e salvar a definição da versão

1. Edite o nome da definição de versão com base no nome especificado na guia **Ações de Pós-build** do build no Jenkins. O Jenkins exige esse nome para poder disparar uma nova versão quando os artefatos de origem forem atualizados.

1. Como opção, altere o nome do ambiente clicando no nome. 

1. Escolha **Salvar** e depois **OK**.

## <a name="start-a-manual-deployment"></a>Iniciar uma implantação manual

1. Escolha **+ Versão** e selecione **Criar Versão**.

1. Selecione o build concluído na lista suspensa realçada e escolha **Criar**.

1. Escolha o link de versão na mensagem pop-up. Por exemplo: "A versão **Release-1** foi criada".

1. Abra a guia **Logs** para observar a saída do console de versão.

1. No navegador, abra a URL de um dos servidores adicionados ao seu grupo de implantação. Por exemplo, insira: `http://{your-server-ip-address}`

## <a name="start-a-cicd-deployment"></a>Iniciar uma implantação de CI/CD

1. Na definição de versão, desmarque a caixa de seleção **Habilitado** na seção **Opções de Controle** das configurações da tarefa Implantação do Grupo de Recursos do Azure.
   Para implantações futuras no grupo de implantação existente, não será necessário executar essa tarefa novamente.

1. Acesse o repositório do Git de origem e modifique o conteúdo do título **h1** no arquivo [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade).

1. Confirme a alteração.

1. Depois de alguns minutos, você verá uma nova versão criada na página **Versões** do Team Services ou TFS. Abra a versão para ver a implantação acontecendo. Parabéns!

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você automatizou a implantação de um aplicativo no Azure usando o build do Jenkins e o Team Services para versão. Você aprendeu como:

> [!div class="checklist"]
> * Compilar seu aplicativo no Jenkins
> * Configurar o Jenkins para integração com o Team Services
> * Criar um grupo de implantação para máquinas virtuais do Azure
> * Criar uma definição de versão que configura as VMs e implanta o aplicativo

Avance para o próximo tutorial para saber mais sobre como implantar uma pilha LAMP (Linux, Apache, MySQL e PHP).

> [!div class="nextstepaction"]
> [Implantar a pilha LAMP](tutorial-lamp-stack.md)
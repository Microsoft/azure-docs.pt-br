---
title: Tutorial – CI/CD para VMs do Azure usando o Azure Pipelines
description: Neste tutorial, você aprende a configurar a CI (integração contínua) e a CD (implantação contínua) de um aplicativo Node.js para VMs do Azure usando o pipeline do Azure baseado em YAML.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 49282bf6cbc7c24b75fbe3f1bbe68bd1fac62ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552483"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Tutorial: Implantar seu aplicativo em máquinas virtuais do Linux no Azure usando o Azure DevOps Services e o Azure Pipelines

A CI (integração contínua) e a CD (implantação contínua) formam um pipeline por meio do qual você pode criar, lançar e implantar seu código após cada commit. Este documento contém as etapas associadas à configuração de um pipeline de CI/CD para fazer implantações em vários computadores usando o Azure Pipelines.

O Azure Pipelines fornece um conjunto completo de ferramentas de automação de CI/CD para implantações em máquinas virtuais, tanto localmente quanto em qualquer nuvem.

Neste tutorial, você configurará um pipeline de CI/CD baseado em YAML para implantar seu aplicativo em um [ambiente do Azure Pipelines](/azure/devops/pipelines/process/environments) com máquinas virtuais do Linux como recursos, cada uma das quais servirá como um servidor Web para executar o aplicativo.

Você aprenderá como:

> [!div class="checklist"]
> * Obter um aplicativo de exemplo.
> * Criar um pipeline de CI do Azure Pipelines baseado em YAML para criar o aplicativo de exemplo.
> * Criar um ambiente do Azure Pipelines para as máquinas virtuais do Azure
> * Criar um pipeline de CD do Azure Pipelines.
> * Execute implantações manuais e disparadas por CI.

## <a name="before-you-begin"></a>Antes de começar

* Entre em sua organização do Azure DevOps Services ( **https://dev.azure.com/** ). 
  Você precisa obter uma [organização do Azure DevOps Services gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para saber mais, confira [Conectar-se ao Azure DevOps Services](/azure/devops/organizations/projects/connect-to-projects).

*  É necessária uma máquina virtual Linux para um destino de implantação.  Para obter mais informações, consulte [Criar e gerenciar VMs Linux com a CLI do Azure](./tutorial-manage-vm.md).

*  Abra a porta de entrada 80 para sua máquina virtual. Para obter mais informações, consulte [Criar grupos de segurança de rede usando o Portal do Azure](../../virtual-network/tutorial-filter-network-traffic.md).

## <a name="get-your-sample-app-code"></a>Obtenha seu código do aplicativo de exemplo

Se já tiver um aplicativo no GitHub que deseja implantar, você poderá tentar criar um pipeline para esse código.

No entanto, se você for um novo usuário, poderá começar melhor usando nosso código de exemplo. Nesse caso, crie um fork deste repositório no GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> O Petclinic é um aplicativo [Java Spring boot](https://spring.io/guides/gs/spring-boot) criado usando o [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Este aplicativo Node.js foi criado por meio do [Yeoman](https://yeoman.io/learning/index.html). Ele usa o Express, Bower e o Grunt. Além disso, ele tem alguns pacotes npm como dependências.
> O exemplo também contém um script que configura o Nginx e implanta o aplicativo. Ele é executado nas máquinas virtuais. Especificamente, o script:
> 1. Instala o Node, Nginx e PM2.
> 2. Configura o Nginx e o PM2.
> 3. Inicia o aplicativo Node.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Pré-requisitos para a VM Linux

Os aplicativos de exemplo mencionados acima foram testados no Ubuntu 16.04. Recomendamos que, para este início rápido, você use a mesma versão da VM do Linux.
Siga as etapas adicionais descritas abaixo com base na pilha de runtime usada para o aplicativo.

#### <a name="java"></a>[Java](#tab/java)

- Para implantar aplicativos baseados em Java Spring Boot e Spring Cloud, crie uma VM do Linux no Azure usando [este](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) modelo, que fornece um runtime baseado em OpenJDK totalmente compatível.
- Para implantar Servlets Java no servidor Tomcat, crie uma VM do Linux com o Java 8 usando [este](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) modelo do Azure e [configure o Tomcat 9.x como um serviço](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Para implantar o aplicativo baseado em Java EE, use um modelo do Azure para criar uma [VM do Linux + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) ou [uma VM do Linux + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) ou uma [VM do Linux + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Para instalar um aplicativo JavaScript ou Node.js, será necessário obter uma VM do Linux com o servidor Web Nginx para implantar o aplicativo.
Se você ainda não tiver uma VM do Linux com Nginx, crie uma agora no Azure usando as etapas [neste](./quick-create-cli.md) exemplo.

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Criar um ambiente do Azure Pipelines com máquinas virtuais do Azure

As máquinas virtuais podem ser adicionadas como recursos em [ambientes](/azure/devops/pipelines/process/environments) e podem ser direcionadas para implantações em vários computadores. As exibições do histórico de implantação no ambiente fornecem capacidade de rastreamento da VM para o pipeline e, em seguida, para o commit.

Você pode criar um ambiente no Hub "**Ambientes**" na seção "**Pipelines**".
1.  Entre na sua organização do Azure DevOps e navegue até seu projeto.
2.  Em seu projeto, navegue até a página **Pipelines**. Em seguida, escolha **Ambientes** e clique em **Criar Ambiente**. Especifique um **Nome** (obrigatório) para o ambiente e uma **Descrição**.
3.  Escolha **Máquinas Virtuais** como um **Recurso** a ser adicionado ao ambiente e clique em **Avançar**.
4.  Escolha o sistema operacional (Windows/Linux) e **copie o script de registro do PS**. 
5.  Agora, execute o script copiado de um prompt de comando com privilégios de administrador do PowerShell em cada uma das VMs de destino a serem registradas com esse ambiente.
    > [!NOTE]
    > - O token de acesso pessoal do usuário conectado é previamente inserido no script que expira no mesmo dia, fazendo com que o script copiado fique inutilizável desse ponto em diante.
    > - Se sua VM já tiver algum agente em execução, forneça um nome exclusivo para o "agente" a registrar no ambiente.
6.  Depois que a VM for registrada, ela começará a aparecer como um recurso de ambiente na guia "recursos" do ambiente.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Para adicionar mais VMs, você pode exibir e copiar o script novamente clicando em "Adicionar recurso" e escolhendo "Máquinas Virtuais" como recurso. Esse script permaneceria o mesmo para que todas as VMs fossem adicionadas a esse ambiente. 
8.  Cada computador interage com o Azure Pipelines para coordenar a implantação do seu aplicativo.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. É possível adicionar marcas à VM como parte do script de registro interativo do PowerShell ou adicionar/remover esse script do modo de exibição de recursos, clicando no ícone de três pontos disponível no final de cada recurso da VM no modo de exibição de recursos.

   As marcas atribuídas permitem que você limite a implantação a máquinas virtuais específicas quando o ambiente é usado em um trabalho de implantação. As marcas são limitadas a 256 caracteres, mas não há nenhum limite para o número de marcas que você pode usar.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definir o pipeline de build de CI

Você precisará de um pipeline de build de CI (integração contínua) que publica seu aplicativo Web, bem como um script de implantação que pode ser executado localmente no servidor Ubuntu. Configure um pipeline de build de CI com base no runtime que você deseja usar. 

1. Entre na sua organização do Azure DevOps e navegue até seu projeto.

1. Em seu projeto, navegue até a página **Pipelines**. Em seguida, escolha a ação para criar um novo pipeline.

1. Percorra as etapas do assistente selecionando primeiro **GitHub** como o local do código-fonte.

1. Você pode ser redirecionado para o GitHub para então entrar. Nesse caso, insira suas credenciais do GitHub.

1. Quando a lista de repositórios for exibida, selecione o repositório de aplicativo de exemplo que desejar.

1. O Azure Pipelines analisará seu repositório e recomendará um modelo de pipeline adequado.

#### <a name="java"></a>[Java](#tab/java)

Selecione o modelo **inicial** e copie o snippet de código YAML abaixo que cria seu projeto Java e executa testes com o Apache Maven:

```YAML
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Para obter mais diretrizes, siga as etapas mencionadas em [Crie seu aplicativo Java com o Maven](/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Selecione o modelo **inicial** e copie o snippet de código YAML abaixo que cria um projeto Node.js geral com o npm.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Para obter mais diretrizes, siga as etapas em [Crie seu aplicativo Node.js com o gulp](/azure/devops/pipelines/ecosystems/javascript).

- Dê uma olhada no pipeline para compreender o que ele faz. Verifique se todas as entradas padrão são apropriadas para seu código.

- Clique em **Salvar e executar** e selecione **Fazer commit diretamente para a ramificação principal**. Depois clique de novo em **Salvar e executar**.

- Uma nova execução é iniciada. Aguarde a conclusão da execução.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definir as etapas da CD para implantar na VM do Linux

1. Altere o arquivo YAML do pipeline acima para incluir um [trabalho de implantação](/azure/devops/pipelines/process/deployment-jobs) referenciando o ambiente e os recursos da VM que você usou anteriormente com a sintaxe YAML abaixo:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. Você pode selecionar conjuntos específicos de máquinas virtuais do ambiente para receber a implantação, especificando as **marcas** que você definiu para cada máquina virtual no ambiente.
Confira [aqui](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) o esquema completo do YAML para o trabalho de implantação.

3. É possível especificar `runOnce` ou `rolling` como a estratégia de implantação. 

   `runOnce` é a estratégia de implantação mais simples, em que todos os ganchos de ciclo de vida, especificamente `preDeploy` `deploy`, `routeTraffic` e `postRouteTraffic`, são executados uma vez. Em seguida, `on:` `success` ou `on:` `failure` é executado.

   Veja abaixo o snippet de código YAML de exemplo para `runOnce`:
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Abaixo está um exemplo do snippet de código YAML que você pode usar para definir uma estratégia sem interrupção para atualizações de máquinas virtuais para até cinco destinos em cada iteração. `maxParallel` determinará o número de destinos para os quais as implantações podem ocorrer paralelamente. As contas de seleção para um número absoluto ou um percentual de destinos que devem permanecer disponíveis a qualquer momento, excluindo os destinos para os quais as implantações estão sendo realizadas. Ele também é usado para determinar as condições de êxito e falha durante a implantação.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Com cada execução desse trabalho, o histórico de implantação é registrado no ambiente de `<environment name>` que você criou e no qual registrou as VMs.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Executar seu pipeline e obter exibições de capacidade de rastreamento no ambiente
A exibição de implantações do ambiente fornece uma capacidade completa de rastreamento de commits e itens de trabalho, bem como um histórico de implantação entre pipelines por ambiente/recurso.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Próximas etapas
- Você pode prosseguir para [personalizar o pipeline](/azure/devops/pipelines/customize-pipeline) que você acabou de criar.
- Para saber o que mais você pode fazer em pipelines do YAML, confira [referência de esquema do YAML](/azure/devops/pipelines/yaml-schema).
- Para saber mais sobre como implantar uma pilha LAMP (Linux, Apache, MySQL e PHP), avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Implantar a pilha LAMP](tutorial-lamp-stack.md)

---
title: Criar um pipeline de CI/CD no Azure com o Team Services | Microsoft Docs
description: "Saiba como criar um pipeline do Visual Studio Team Services para integração e entrega contínuas que implanta um aplicativo Web do IIS em uma VM do Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a587f58fad2ec74c7633823c4d34f900e7c01f7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Criar um pipeline de integração contínua com o Visual Studio Team Services e o IIS
Para automatizar as fases de compilação, teste e implantação do desenvolvimento de aplicativos, você pode usar um pipeline de CI/CD (implantação e integração contínuas). Neste tutorial, você cria um pipeline de CI/CD usando o Visual Studio Team Services e uma VM (máquina virtual) do Windows no Azure que executa o IIS. Você aprenderá como:

> [!div class="checklist"]
> * Publicar um aplicativo Web ASP.NET em um projeto de Equipe de Serviços
> * Criar uma definição de build disparada por confirmações de código
> * Instalar e configurar o IIS em uma máquina virtual no Azure
> * Adicionar a instância do IIS a um grupo de implantação no Team Services
> * Criar uma definição de versão para publicar novos pacotes de implantação Web no IIS
> * Testar o pipeline de CI/CD

Este tutorial requer o módulo do Azure PowerShell, versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="create-project-in-team-services"></a>Criar o projeto no Team Services
O Visual Studio Team Services facilita a colaboração e o desenvolvimento sem manter uma solução de gerenciamento de código local. O Team Services oferece compilação, código de testes e Application Insights. Você pode escolher um repositório de controle de versão e o IDE que melhor se adapta a seu desenvolvimento de código. Para este tutorial, você pode usar uma conta gratuita para criar um aplicativo Web ASP.NET básico e o pipeline de CI/CD. Se você ainda não tem uma conta do Team Services, [crie uma](http://go.microsoft.com/fwlink/?LinkId=307137).

Para gerenciar o processo de confirmação do código, as definições de compilação e as definições de versão, crie um projeto no Team Services da seguinte maneira:

1. Abra o painel Team Services em um navegador da Web e escolha **Novo projeto**.
2. Digite *myWebApp* como **Nome do projeto**. Deixe todos os outros valores padrão para usar o controle de versão do *Git* e o processo de item de trabalho *Agile*.
3. Escolha a opção de **Compartilhar com** *Membros da Equipe* e selecione **Criar**.
5. Quando o projeto tiver sido criado, escolha a opção de **Inicializar com um arquivo LEIAME ou gitignore** e **Inicializar**.
6. Dentro de seu novo projeto, escolha **Painéis** na parte superior e selecione **Abrir no Visual Studio**.


## <a name="create-aspnet-web-application"></a>Criar aplicativo Web ASP.NET
Na etapa anterior, você criou um projeto no Team Services. A etapa final abre seu novo projeto no Visual Studio. Gerencie suas confirmações de código na janela **Team Explorer**. Crie uma cópia local do novo projeto e crie um aplicativo Web ASP.NET de um modelo da seguinte maneira:

1. Selecione **Clonar** para criar um repositório git local de seu projeto do Team Services.
    
    ![Clonar o repositório do projeto do Team Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Em **Soluções**, selecione **Novo**.

    ![Criar a solução do aplicativo Web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Selecione modelos **Web** e escolha o modelo **Aplicativo Web ASP.NET**.
    1. Insira um nome para seu aplicativo, como *myWebApp*e desmarque a caixa **Criar diretório para a solução**.
    2. Se a opção estiver disponível, desmarque a caixa para **Adicionar o Application Insights ao projeto**. O Application Insights exige que você autorize seu aplicativo Web com o Azure Application Insights. Para simplificar este tutorial, ignore esse processo.
    3. Selecione **OK**.
4. Escolha **MVC** na lista de modelos.
    1. Selecione **Alterar Autenticação**, escolha **Sem Autenticação** e selecione **OK**.
    2. Selecione **OK** para criar sua solução.
5. Na janela **Team Explorer**, escolha **Alterações**.

    ![Confirmar alterações locais no repositório git do Team Services](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. Na caixa de texto de confirmação, digite uma mensagem como *Confirmação inicial*. Escolha **Confirmar Tudo e Sincronizar** no menu suspenso.


## <a name="create-build-definition"></a>Criar definição de build
No Team Services, você pode usar uma definição de compilação para descrever como seu aplicativo deve ser criado. Neste tutorial, podemos criar uma definição básica que usa nosso código-fonte, compila a solução e cria o pacote de implantação na Web que podemos usar para executar o aplicativo Web em um servidor IIS.

1. Em seu projeto do Team Services, escolha **Compilação e Versão** na parte superior e selecione **Versões**.
3. Selecione **+ Nova definição**.
4. Escolha o modelo **ASP.NET (VERSÃO PRÉVIA)** e selecione **Aplicar**.
5. Deixe o padrão de todos os valores de tarefa. Em **Obter fontes**, verifique se o repositório *myWebApp* e a ramificação *mestre* estão selecionados.

    ![Criar definição de build no projeto do Team Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Na guia **Gatilhos**, mova o controle deslizante de **Habilitar esse disparador** para *Habilitado*.
7. Salve a definição da build e coloque na fila uma nova build selecionando **Salvar e enfileirar** e novamente **Salvar e enfileirar**. Deixe os padrões e selecione **Enfileirar**.

Veja como a build fica agendada em um agente hospedado e começa a ser criada. A saída deverá ser semelhante ao seguinte exemplo:

![Compilação bem-sucedida do projeto do Team Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Criar máquina virtual
Para oferecer uma plataforma de execução do seu aplicativo Web ASP.NET, você precisará de uma máquina virtual Windows que execute o IIS. O Team Services usa um agente para interagir com a instância do IIS durante a confirmação do código e os disparos das compilações.

Criar uma VM do Windows Server 2016 usando [este exemplo de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json). Demora alguns minutos para que o script execute e crie a VM. Quando a VM for criada, abra a porta 80 para tráfego da Web com [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) da seguinte maneira:

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

Para se conectar à sua VM, obtenha o endereço IP público com [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) da seguinte maneira:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

Crie uma sessão de área de trabalho remota para sua VM:

```cmd
mstsc /v:<publicIpAddress>
```

Na VM, abra um prompt de comando do **Administrador do PowerShell**. Instale o IIS e os recursos necessários do .NET da seguinte maneira:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Criar grupo de implantação
Para enviar o pacote de implantação Web por push pacote para o servidor IIS, defina um grupo de implantação no Team Services. Esse grupo permite que você especifique quais servidores são o destino das novas builds conforme você confirma o código para o Team Services e as builds são concluídas.

1. No Team Services, escolha **Compilação e Versão** e selecione **Grupos de implantação**.
2. Escolha **Adicionar Grupo de implantação**.
3. Insira um nome para o grupo, como *myIIS* e selecione **Criar**.
4. Na seção **Registrar máquinas**, verifique se *Windows* foi selecionado e marque a caixa **Usar um token de acesso pessoal no script para autenticação**.
5. Selecione **Copiar o script para área de transferência**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Adicionar VM do IIS ao grupo de implantação
Com o grupo de implantação criado, adicione cada instância do IIS ao grupo. O Team Services gera um script que baixa e configura um agente na VM para receber os novos pacotes de implantação Web e aplicá-los ao IIS.

1. De volta à sessão **Administrador do PowerShell** na sua VM, cole e execute o script copiado do Team Services.
2. Quando solicitado a configurar marcações para o agente, escolha *Y* e digite *Web*.
3. Quando a conta de usuário for solicitada, pressione *Retornar* para aceitar os padrões.
4. Aguarde o script terminar com uma mensagem *Serviço vstsagent.account.computername de serviço iniciado com êxito*.
5. Na página **Grupos de implantação** do menu **Compilação e Versão**, abra o grupo de implantação *myIIS*. Na guia **Máquinas**, verifique se que a sua VM está listada.

    ![VM adicionada com êxito ao grupo de implantação do Team Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Criar definição de versão
Para publicar suas builds, crie uma definição de versão no Team Services. Essa definição é disparada automaticamente pela compilação bem-sucedida do seu aplicativo. Escolha o grupo de implantação para enviar seu pacote de implantação Web e defina as configurações do IIS apropriadas.

1. Escolha **Compilação e Versão** e selecione **Builds**. Escolha a definição de compilação criada na etapa anterior.
2. Em **Concluído recentemente**, escolha a compilação mais recente e selecione **Versão**.
3. Escolha **Sim** para criar uma definição de versão.
4. Escolha o modelo **Vazio** e selecione **Avançar**.
5. Verifique se a definição de build do projeto e de origem estão preenchidas com seu projeto.
6. Marque a caixa de seleção **Implantação contínua** e selecione **Criar**.
7. Selecione a caixa com lista suspensa próxima a **+ Adicionar tarefas** e escolha *Adicionar uma fase do grupo de implantação*.
    
    ![Adicionar tarefa a uma definição de versão no Team Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Escolha **Adicionar** ao lado de **Implantação de aplicativo Web IIS (Versão Prévia)** e selecione **Fechar**.
9. Selecione a tarefa pai **Executar no grupo de implantação**.
    1. Em **Grupo de Implantação**, selecione o grupo de implantação que você criou anteriormente, como *myIIS*.
    2. Na caixa **Marcações da máquina**, selecione **Adicionar** e escolha a marcação *Web*.
    
    ![Tarefa de grupo de implantação da definição de versão para o IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Selecione a tarefa **Implantar: implantar o aplicativo Web IIS** para definir as configurações de instância do IIS da seguinte maneira:
    1. Em **nome do site**, digite *Site Padrão*.
    2. Deixe todas as outras configurações padrão.
12. Escolha **Salvar** e selecione **OK** duas vezes.


## <a name="create-a-release-and-publish"></a>Criar uma versão e publicar
Agora você pode enviar seu pacote de implantação Web como uma nova versão. Esta etapa se comunica com o agente em cada instância que é parte do grupo de implantação, envia ao pacote de implantação Web por push e configura o IIS para executar o aplicativo Web atualizado.

1. Em sua definição de versão, selecione **+ Versão** e escolha **Criar Versão**.
2. Verifique se a compilação mais recente está selecionada na lista suspensa, juntamente com **Implantação automatizada: após a criação da versão**. Selecione **Criar**.
3. Uma pequena faixa aparece na parte superior da sua definição de versão, como *A versão 'Versão-1' foi criada*. Selecione o link da versão.
4. Abra a guia **Logs** para observar o andamento da versão.
    
    ![Versão do Team Services bem-sucedida e envio do pacote de implantação Web por push](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Após a conclusão da versão, abra um navegador da Web e digite o endereço IIP público da sua VM. Seu aplicativo Web ASP.NET está em execução.

    ![Aplicativo Web ASP.NET em execução na VM do IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testar o pipeline de CI/CD inteiro
Com o aplicativo Web em execução no IIS, experimente o pipeline de CI/CD inteiro. Depois de fazer uma alteração no Visual Studio e confirmar seu código, uma compilação será disparada e disparará uma versão do seu pacote de implantação Web atualizado para o IIS:

1. No Visual Studio, abra a janela **Gerenciador de Soluções**.
2. Navegue até e abra *myWebApp | Modos de Exibição | Página Inicial | Index. cshtml*
3. Edite a linha 6 para conter o seguinte:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Salve o arquivo.
5. Abra a janela **Team Explorer**, selecione o projeto *myWebApp* e escolha **Alterações**.
6. Insira uma mensagem de confirmação, como *Testando pipeline de CI/CD*, escolha **Confirmar Todos e Sincronizar** no menu suspenso.
7. No espaço de trabalho do Team Services, uma nova build é disparada com a confirmação do código. 
    - Escolha **Compilação e Versão** e selecione **Builds**. 
    - Escolha sua definição de build e selecione a build **Na fila e em execução** para assistir ao andamento da compilação.
9. Depois que a compilação é bem-sucedida, uma nova versão é disparada.
    - Escolha **Compilação e Versão** e **Versões** para ver o pacote de implantação Web enviado por push para a VM do IIS. 
    - Selecione o ícone **Atualizar** para atualizar o status. Quando a coluna *Ambientes* mostra uma marca de seleção verde, a versão foi implantada com êxito no IIS.
11. Para ver as alterações aplicadas, atualize seu site do IIS em um navegador.

    ![Aplicativo Web ASP.NET em execução na VM do IIS pelo pipeline de CI/CD](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo Web ASP.NET no Team Services e configurou definições de build e versão para implantar novos pacotes de implantação Web no IIS em cada confirmação de código. Você aprendeu como:

> [!div class="checklist"]
> * Publicar um aplicativo Web ASP.NET em um projeto de Equipe de Serviços
> * Criar uma definição de build disparada por confirmações de código
> * Instalar e configurar o IIS em uma máquina virtual no Azure
> * Adicionar a instância do IIS a um grupo de implantação no Team Services
> * Criar uma definição de versão para publicar novos pacotes de implantação Web no IIS
> * Testar o pipeline de CI/CD

Vá para o próximo tutorial para saber como proteger um servidor Web com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor Web com SSL](tutorial-secure-web-server.md)
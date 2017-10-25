---
title: "Implantação de máquina virtual do Azure com o Chef | Microsoft Docs"
description: "Saiba como usar o Chef para realizar implantação e configuração automatizadas da máquina virtual no Microsoft Azure"
services: virtual-machines-windows
documentationcenter: 
author: diegoviso
manager: timlt
tags: azure-service-management,azure-resource-manager
editor: 
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: b6db0fbb4e0de896994954974ddcc39daad9c125
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizando a implantação de máquina virtual do Azure com o Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

O Chef é uma excelente ferramenta para a entrega de automação e configurações de estado de desejado.

Com nossa versão mais recente da api de nuvem, o Chef fornece integração perfeita com o Azure, dando a você a capacidade de provisionar e implantar os estados de configuração por meio de um único comando.

Neste artigo, mostrarei a você como configurar o ambiente do Chef para provisionar máquinas virtuais do Azure e a criar uma política ou um "Guia" e, em seguida, implantar esse guia em uma máquina virtual do Azure.

Vamos começar!

## <a name="chef-basics"></a>Noções básicas do Chef
Antes de começar, sugiro que você leia os conceitos básicos do Chef. Há um excelente material <a href="http://www.chef.io/chef" target="_blank">aqui</a> e recomendo que você leia rapidamente antes de tentar este passo-a-passo. No entanto, irei recapitular os conceitos básicos antes de começarmos.

O diagrama a seguir ilustra a arquitetura de alto nível do Chef.

![][2]

O Chef tem três componentes principais de arquitetura: Chef Server, Chef Client (nó) e Chef Workstation.

O Chef Server é o nosso ponto de gerenciamento e há duas opções para o Chef Server: uma solução hospedada ou uma solução local. Vamos usar uma solução hospedada.

O Chef Client (nó) é o agente que reside nos servidores que você está gerenciando.

O Chef Workstation é nossa estação de trabalho administrativa onde criamos nossas políticas e executamos os comandos de gerenciamento. Executamos o comando **knife** do Chef Workstation para gerenciar nossa infraestrutura.

Há também o conceito de “Guias" e "Receitas". Estas são efetivamente as políticas que definimos e aplicamos aos nossos servidores.

## <a name="preparing-the-workstation"></a>Preparando a estação de trabalho
Primeiro, vamos preparar a estação de trabalho. Estou usando uma estação de trabalho padrão do Windows. É necessário criar um diretório para armazenar os arquivos de configuração e guias.

Primeiro, crie um diretório chamado C:\chef.

Em seguida, crie um segundo diretório chamado c:\chef\cookbooks.

Agora, é necessário baixar o arquivo de configurações do Azure para que o Chef possa se comunicar com a nossa assinatura do Azure.

<!--Download your publish settings from [here](https://manage.windowsazure.com/publishsettings/).-->
Baixe as configurações de publicação usando o comando [AzurePublishSettingsFile Get](https://docs.microsoft.com/en-us/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) do PowerShell Azure. 

Salve o arquivo de configurações de publicação em C:\chef.

## <a name="creating-a-managed-chef-account"></a>Criando uma conta gerenciada do Chef
Inscreva-se para uma conta hospedada do Chef [aqui](https://manage.chef.io/signup).

Durante o processo de inscrição, você será solicitado a criar uma nova organização.

![][3]

Depois de criar sua organização, baixe o kit inicial.

![][4]

> [!NOTE]
> Se você receber um aviso informando que as suas chaves serão redefinidas, está tudo bem prosseguir, visto que não temos nenhuma infraestrutura existente configurada ainda.
> 
> 

Este arquivo zip do kit inicial contém arquivos de configuração de organização e chaves.

## <a name="configuring-the-chef-workstation"></a>Configurando o Chef Workstation
Extraia o conteúdo do chef-starter.zip em C:\chef.

Copie todos os arquivos em chef-starter\chef-repo\.chef no seu diretório c:\chef.

O diretório agora deve ser semelhante ao exemplo a seguir.

![][5]

Agora você deve ter quatro arquivos, incluindo o arquivo de publicação do Azure na raiz do c:\chef.

Os arquivos PEM contêm as chaves particulares de organização e administração para comunicação enquanto o arquivo knife.rb contém a configuração knife. Será necessário editar o arquivo knife.rb.

Abra o arquivo no seu editor de escolha e modifique o "cookbook_path" removendo o /../ do caminho para que ele se pareça como mostrado a seguir.

    cookbook_path  ["#{current_dir}/cookbooks"]

Além disso, adicione a seguinte linha para refletir o nome do seu arquivo de publicação do Azure.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

O arquivo knife.rb agora deve ser semelhante ao exemplo a seguir.

![][6]

Essas linhas garantirão as referências knife em nosso diretório de guias c:\chef\cookbooks e também usam o arquivo Configurações de Publicação do Azure durante as operações do Azure.

## <a name="installing-the-chef-development-kit"></a>Instale o Kit de desenvolvimento Chef
Em seguida [baixe e instale](http://downloads.getchef.com/chef-dk/windows) o ChefDK (Chef Development Kit) para configurar seu Chef Workstation.

![][7]

Instale-o no local padrão c:\opscode. Esta instalação levará cerca de 10 minutos.

Confirme que sua variável PATH contém entradas para C:\opscode\chefdk\bin;C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Se eles não estão lá, certifique-se que adicionar esses caminhos!

*OBSERVE QUE A ORDEM DO CAMINHO É IMPORTANTE!* Se seus caminhos opscode não estiverem na ordem correta, você terá problemas.

Reinicie a estação de trabalho antes de continuar.

Em seguida, instalaremos a extensão do Knife Azure. Isso fornece Knife com o "plug-in Azure".

Execute o comando a seguir.

    chef gem install knife-azure ––pre

> [!NOTE]
> O argumento –pre garante que você está recebendo a última versão do RC do Plug-in Knife Azure, que fornece acesso ao conjunto mais recente de APIs.
> 
> 

É provável que um número de dependências também seja instalado ao mesmo tempo.

![][8]

Para garantir que tudo esteja configurado corretamente, execute o comando a seguir.

    knife azure image list

Se tudo estiver configurado corretamente, você verá uma lista de imagens do Azure disponíveis rolar.

Parabéns. O Workstation está configurado!

## <a name="creating-a-cookbook"></a>Criação de um Guia
Um Guia é usado pelo Chef para definir um conjunto de comandos que você deseja executar no cliente gerenciado. Criar um Guia é muito simples e direto e usamos o comando **chef generate cookbook** para gerar nosso modelo de Guia. Eu chamarei meu servidor Web do Guia como eu faria com a política que implanta automaticamente o IIS.

No diretório C:\Chef, execute o comando a seguir.

    chef generate cookbook webserver

Isso irá gerar um conjunto de arquivos no diretório C:\Chef\cookbooks\webserver. Agora, precisamos definir o conjunto de comandos que gostaríamos que nosso Chef Client executasse em nossa máquina virtual gerenciada.

Os comandos são armazenados no arquivo default.rb. Nesse arquivo, estarei definindo um conjunto de comandos que instala o IIS, inicia o IIS e copia um arquivo de modelo para a pasta wwwroot.

Modifique o arquivo C:\chef\cookbooks\webserver\recipes\default.rb e adicione as linhas a seguir.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Quando terminar, salve o arquivo.

## <a name="creating-a-template"></a>Criando um modelo
Conforme mencionado anteriormente, é necessário gerar um arquivo de modelo que será usado como nossa página default.html.

Execute o comando a seguir para gerar o modelo.

    chef generate template webserver Default.htm

Agora, navegue para o arquivo C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Edite o arquivo adicionando um código HTML simples "Hello World" e salve o arquivo.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Carregue o Guia no Chef Server
Nesta etapa, estamos fazendo uma cópia do Guia que criamos na máquina local e carregando no servidor hospedado do Chef. Uma vez carregado, o Guia aparecerá sob a guia **Política** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implantar uma máquina virtual com o Knife Azure
Agora podemos implantar uma máquina virtual do Azure e aplicar o Guia "Webserver", que instalará nossa página da Web padrão e o serviço Web do IIS.

Para fazer isso, use o comando **knife azure server create** .

Veja a seguir, um exemplo do comando.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Os parâmetros são autoexplicativos. Substitua variáveis específicas e executar.

> [!NOTE]
> Por meio da linha de comando, também estou automatizando minhas regras de filtro de rede do ponto de extremidade usando o parâmetro –tcp-endpoints. Eu abri as portas 80 e 3389 para fornecer acesso a minha página da Web e à sessão RDP.
> 
> 

Depois de executar o comando, vá para o portal do Azure e você verá o início do provisionamento de seu computador.

![][13]

O prompt de comando aparecem a seguir.

![][10]

Depois que a implantação estiver concluída, devemos ser capazes de nos conectarmos ao serviço web pela porta 80, visto que abrimos a porta quando provisionamos a máquina virtual com o comando Knife Azure. Como essa máquina virtual é a única em meu serviço de nuvem, vou conectá-la à url do serviço de nuvem.

![][11]

Como você pode ver, fui criativo com meu código HTML.

Não se esqueça de que também podemos nos conectar através de uma sessão RDP do portal do Azure por meio da porta 3389.

Espero que isso tenha sido útil! Siga adiante e comece sua jornada de infraestrutura como código com o Azure hoje mesmo!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png


<!--Link references-->

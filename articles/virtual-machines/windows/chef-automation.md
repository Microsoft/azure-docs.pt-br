---
title: Implantação de máquina virtual do Azure com o Chef | Microsoft Docs
description: Saiba como usar o Chef para realizar implantação e configuração automatizadas da máquina virtual no Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 74b92c277b1d6eaa0984e55a70459bad59c2bf84
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719282"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizando a implantação de máquina virtual do Azure com o Chef

O Chef é uma excelente ferramenta para a entrega de automação e configurações de estado de desejado.

Com a última versão da API de nuvem, o Chef fornece integração perfeita com o Azure, dando a você a capacidade de provisionar e implantar os estados de configuração por meio de um único comando.

Neste artigo, você configura o ambiente do Chef para provisionar máquinas virtuais do Azure e percorre as etapas de criação de uma política ou um "Guia" e de implantação desse guia em uma máquina virtual do Azure.

## <a name="chef-basics"></a>Noções básicas do Chef
Antes de começar, [leia os conceitos básicos do Chef](https://www.chef.io/chef).

O diagrama a seguir ilustra a arquitetura de alto nível do Chef.

![][2]

O Chef tem três componentes de arquitetura principais: O Chef Server, o Chef Client (nó) e o Chef Workstation.

O Chef Server é o ponto de gerenciamento e há duas opções para o Chef Server: uma solução hospedada ou uma solução local.

O Chef Client (nó) é o agente que reside nos servidores que você está gerenciando.

O Chef Workstation, que é o nome tanto da estação de trabalho do administrador em que você cria políticas e executa comandos de gerenciamento quanto do pacote de software das ferramentas do Chef.

Em geral, você verá _sua estação de trabalho_ como a localização em que você pode executar ações e o _Chef Workstation_ como o pacote de software.
Por exemplo, você baixará o comando knife como parte do _Chef Workstation_, mas você executar comandos knife do _sua estação de trabalho_ para gerenciar a infraestrutura.

O Chef também usa os conceitos de "Guias" e "Receitas", que são efetivamente as políticas que definimos e se aplicam aos servidores.

## <a name="preparing-your-workstation"></a>Preparando sua estação de trabalho

Primeiro, prepare sua estação de trabalho criando um diretório para armazenar os arquivos de configuração e guias do Chef.

Crie um diretório chamado c:\chef.

Baixe e instale a versão mais recente [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão logon em sua estação de trabalho.

## <a name="configure-azure-service-principal"></a>Configurar uma entidade de serviço do Azure

No mais simples de termos e entidade de serviço do Azure é uma conta de serviço.   Vamos usar uma entidade de serviço para nos ajudar a criar recursos do Azure em nossa estação de trabalho do Chef.  Para criar a entidade de serviço relevantes com as permissões necessárias, precisamos executar os seguintes comandos do PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Reserve uma anotação de SubscriptionID, TenantID, ClientID e o segredo do cliente (a senha definida acima), você precisará dele mais tarde. 

## <a name="setup-chef-server"></a>Configurar o Chef Server

Este guia considera que você se inscreverá no Hosted Chef.

Se ainda não estiver usando um Chef Server, você poderá:

* inscrever-se no [Hosted Chef](https://manage.chef.io/signup), que é a maneira mais rápida de começar a usar o Chef.
* instalar um Chef Server autônomo no computador baseado em Linux, seguindo as [instruções de instalação](https://docs.chef.io/install_server.html) em [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Criando uma conta do Hosted Chef

Inscreva-se em uma conta do Hosted Chef [aqui](https://manage.chef.io/signup).

Durante o processo de inscrição, será solicitado que você crie uma organização.

![][3]

Depois de criar sua organização, baixe o kit inicial.

![][4]

> [!NOTE]
> Se você receber um aviso informando de que suas chaves serão redefinidas, não haverá problema em prosseguir, pois ainda não temos uma infraestrutura existente configurada.
>

Este arquivo zip do kit de início contém os arquivos de configuração da organização e a chave do usuário no diretório `.chef`.

O `organization-validator.pem` precisa ser baixado separadamente, pois como é uma chave privada não deve ser armazenado no Chef Server. Partir [Chef gerenciar](https://manage.chef.io/), vá para a seção de administração e selecione "Redefinir chave de validação", que fornece um arquivo para download separadamente. Salve o arquivo em C:\chef.

### <a name="configuring-your-chef-workstation"></a>Configurando sua estação de trabalho do Chef

Extraia o conteúdo do chef-starter.zip para c:\chef.

Copie todos os arquivos em chef-starter\chef-repo\.chef no seu diretório c:\chef.

Copie o arquivo `organization-validator.pem` para C:\chef., se ele estiver salvo em C:\Downloads

O diretório agora deve ser semelhante ao exemplo a seguir.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Agora você deve ter cinco arquivos e quatro diretórios (incluindo o diretório chef-repo vazio) na raiz do C:\chef.

### <a name="edit-kniferb"></a>Editar o knife.rb

Os arquivos PEM contêm as chaves privadas da organização e administrativas para comunicação enquanto o arquivo knife.rb contém a configuração do knife. Será necessário editar o arquivo knife.rb.

Abra o arquivo knife.rb no editor de sua escolha. O arquivo inalterado deve ser semelhante a este:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Adicione as seguintes informações ao seu knife.rb:

validation_client_name   "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Essas linhas garantirão que faca faz referência a diretório de guias em c:\chef\cookbooks e também usa a entidade de serviço do Azure que você criou durante as operações do Azure.

O arquivo knife.rb agora deve estar semelhante ao exemplo a seguir:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Instalar o Chef Workstation

Em seguida, [baixe e instale](https://downloads.chef.io/chef-workstation/) o Chef Workstation.
Instale o Chef Workstation na localização padrão. Essa instalação poderá levar alguns minutos.

Na área de trabalho, você verá um "CW PowerShell", que é um ambiente carregado com a ferramenta que você precisará para interagir com os produtos do Chef. O PowerShell CW disponibiliza novos comandos ad-hoc, como `chef-run` comandos de CLI tradicionais, bem como do Chef, como `chef`. Confira sua versão instalada do Chef Workstation e das ferramentas do Chef com `chef -v`. Você também pode verificar a versão do Workstation selecionando "Sobre o Chef Workstation" no aplicativo Chef Workstation.

`chef --version` deve retornar algo como:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> A ordem do caminho é importante! Se seus caminhos opscode não estiverem na ordem correta, você terá problemas.
>

Reinicie a estação de trabalho antes de continuar.

### <a name="install-knife-azure"></a>Instalar o Knife Azure

Este tutorial confira que você esteja usando o Azure Resource Manager para interagir com sua máquina virtual.

Instale a extensão Knife Azure. Isso fornece Knife com o "plug-in Azure".

Execute o seguinte comando.

    chef gem install knife-azure ––pre

> [!NOTE]
> O argumento –pre garante que você está recebendo a última versão do RC do Plug-in Knife Azure, que fornece acesso ao conjunto mais recente de APIs.
>
>

É provável que um número de dependências também seja instalado ao mesmo tempo.

![][8]

Para garantir que tudo esteja configurado corretamente, execute o comando a seguir.

    knife azurerm server list

Se tudo estiver configurado corretamente, você verá uma lista de imagens do Azure disponíveis rolar.

Parabéns. Sua estação de trabalho está configurada.

## <a name="creating-a-cookbook"></a>Criação de um Guia

Um Guia é usado pelo Chef para definir um conjunto de comandos que você deseja executar no cliente gerenciado. A criação de um guia é simples, basta usar o comando **chef generate cookbook** para gerar o modelo de guia. Este guia é indicado para um servidor Web que implante o IIS automaticamente.

No diretório C:\Chef, execute o comando a seguir.

    chef generate cookbook webserver

Esse comando gera um conjunto de arquivos no diretório C:\Chef\cookbooks\webserver. Em seguida, defina o conjunto de comandos para o cliente do Chef executar na máquina virtual gerenciada.

Os comandos são armazenados no arquivo default.rb. Nesse arquivo, defina um conjunto de comandos que instale o IIS, inicie o IIS e copie um arquivo de modelo para a pasta wwwroot.

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
Nesta etapa, você gerará um arquivo de modelo a ser usado como a página default.html.

Execute o seguinte comando para gerar o modelo:

    chef generate template webserver Default.htm

Navegue até o arquivo `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Edite o arquivo adicionando um código HTML simples "Hello World" e salve o arquivo.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Carregue o Guia no Chef Server
Nesta etapa, você faz uma cópia do guia criado no computador local e carrega-a no servidor do Chef Hosted. Depois de ser carregado, o guia aparecerá na guia **Política**.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implantar uma máquina virtual com o Knife Azure
Implante uma máquina virtual do Azure e aplique o guia "Webserver", que instala o serviço Web IIS e a página da Web padrão.

Para fazer isso, use o **knife azurerm server criar** comando.

Um exemplo do comando aparece a seguir.

    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"


O exemplo acima criará uma máquina virtual de Standard_DS2_v2 com Windows Server 2016 instalado na região Oeste dos EUA. Substitua variáveis específicas e executar.

> [!NOTE]
> Por meio da linha de comando, também estou automatizando minhas regras de filtro de rede do ponto de extremidade usando o parâmetro –tcp-endpoints. Eu abri as portas 80 e 3389 para fornecer acesso à página da web e sessão RDP.
>
>

Depois de executar o comando, acesse o portal do Azure para ver o início do provisionamento da máquina.

![][15]

O prompt de comando aparecem a seguir.

![][16]

Depois que a implantação for concluída, o endereço IP público da nova máquina virtual será ser exibido após a conclusão da implantação, você pode copiar isto e colá-lo em um navegador da web e exibir o site que você implantou. Quando implantamos a máquina virtual abrimos a porta 80 para que ele deve estar disponível externamente.   

![][11]

Este exemplo usa o código HTML criativo.

Você também pode exibir o status do nó [Chef gerenciar](https://manage.chef.io/). 

![][17]

Não se esqueça de que você também pode se conectar por meio de uma sessão RDP do portal do Azure pela porta 3389.

Obrigado! Siga adiante e comece sua jornada de infraestrutura como código com o Azure hoje mesmo!

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
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->

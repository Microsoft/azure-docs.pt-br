---
title: Implantar um aplicativo .NET em um contêiner do Azure Service Fabric
description: Saiba como colocar um aplicativo existente do .NET em um contêiner usando o Visual Studio e depure os contêineres no Service Fabric localmente. O aplicativo colocado em um contêiner é enviado por push para um registro de contêiner do Azure e implantado em um cluster do Service Fabric. Quando implantado no Azure, o aplicativo usa o Banco de Dados SQL do Azure para manter os dados.
ms.topic: tutorial
ms.date: 07/08/2019
ms.openlocfilehash: 7930651a0faa5f37336c15557e2a0f068d613011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936717"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Tutorial: Implantar um aplicativo .NET em um contêiner do Windows no Azure Service Fabric

Este tutorial mostra como colocar um aplicativo ASP.NET existente em um contêiner e empacotá-lo como um aplicativo do Service Fabric.  Execute os contêineres localmente no cluster de desenvolvimento do Service Fabric e, em seguida, implante o aplicativo no Azure.  O aplicativo persiste os dados no [Banco de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Colocar um aplicativo existente em um contêiner usando o Visual Studio
> * Criar um banco de dados no Banco de Dados SQL do Azure
> * Criar um registro de contêiner do Azure
> * Implantar um aplicativo do Service Fabric no Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

1. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Habilite os recursos **Hyper-V** e **Contêineres** do Windows.
3. Instale o [Docker Desktop para Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) de maneira que você possa executar contêineres no Windows 10.
4. Instalar a [versão 6.2 ou posterior de runtime do Service Fabric](service-fabric-get-started.md) e a [versão 3.1 do SDK do Service Fabric](service-fabric-get-started.md) ou posterior.
5. Instale o [Visual Studio 2019 versão 16.1](https://www.visualstudio.com/) ou posterior com as cargas de trabalho de **Desenvolvimento do Azure** e **Desenvolvimento para a Web e ASP.NET**.
6. Instalar o [Azure PowerShell][link-azure-powershell-install]

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Baixar e executar o Fabrikam Fiber CallCenter

1. Baixe o aplicativo de exemplo [Fabrikam Fiber CallCenter][link-fabrikam-github].  Clique no link **baixar arquivo**.  No diretório *sourceCode* no arquivo *fabrikam.zip*, extraia o arquivo *sourceCode.zip* e, em seguida, extraia o diretório *VS2015* no seu computador.

2. Verifique se o aplicativo Fabrikam fibra CallCenter é compilado e executado sem erros.  Inicie o Visual Studio como um **administrador** e abra o arquivo [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Pressione F5 para depurar e executar o aplicativo.

   ![Captura de tela da home page do aplicativo Fabrikam Fiber CallCenter em execução no host local. A página mostra um painel com uma lista de chamadas de suporte.][fabrikam-web-page]

## <a name="create-an-azure-sql-db"></a>Criar um Banco de Dados SQL do Azure

Ao executar o aplicativo da Fabrikam Fiber CallCenter em produção, os dados precisam ser mantidos em um banco de dados. Atualmente, não existe uma maneira de garantir a persistência de dados em um contêiner. Portanto, não é possível armazenar dados de produção no SQL Server em um contêiner.

Recomendamos o [Banco de Dados SQL do Azure](../azure-sql/database/powershell-script-content-guide.md). Para configurar e executar um banco de dados gerenciado do SQL Server no Azure, execute o script a seguir.  Modificar as variáveis de script conforme necessário. *clientIP* é o endereço IP do seu computador de desenvolvimento. Anote o nome do servidor produzido pelo script.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> Se você estiver atrás de um firewall corporativo, o endereço IP do seu computador de desenvolvimento não pode ser um endereço IP exposto à Internet. Para verificar se o banco de dados tem o endereço IP correto para a regra de firewall, vá até [portal do Azure](https://portal.azure.com) e encontre seu banco de dados na seção de bancos de dados SQL. Clique em seu nome, depois, na seção Visão geral, clique em “Definir o firewall do servidor”. O “Endereço IP do cliente” é o endereço IP do seu computador de desenvolvimento. Verifique se ele corresponde ao endereço IP na regra “AllowClient”.

## <a name="update-the-web-config"></a>Atualizar a configuração da Web

Novamente no projeto **FabrikamFiber.Web**, atualize a cadeia de conexão no arquivo **web.config** para que ela aponte para o SQL Server no contêiner.  Atualize a parte do *Servidor* da cadeia de conexão para o nome do servidor criado pelo script anterior. Deve ser algo como "fab-fiber-751718376.database.windows.net". No XML a seguir, você precisa atualizar somente o atributo `connectionString`. Os atributos `providerName` e `name` não precisam ser alterados.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>Você pode usar qualquer SQL Server que preferir para a depuração local, desde que ele seja acessível no host. No entanto, **localdb** não dá suporte à comunicação `container -> host`. Se você desejar usar outro banco de dados SQL ao criar um build de versão de seu aplicativo Web, adicione outra cadeia de conexão ao arquivo *web.release.config*.

## <a name="containerize-the-application"></a>Colocar o aplicativo em um contêiner

1. Clique com o botão direito do mouse no projeto **FabrikamFiber.Web** > **Adicionar** > **Suporte do Orquestrador do Contêiner**.  Selecione **Service Fabric** como o orchestrator do contêiner e clique em **OK**.

2. Se solicitado, clique em **Sim** para alternar o Docker para contêineres do Windows agora.

   Um novo projeto de aplicativo do Service Fabric **FabrikamFiber.CallCenterApplication** é criado na solução.  Um Dockerfile é adicionado ao projeto **FabrikamFiber.Web** existente.  Um diretório **PackageRoot** também é adicionado ao projeto **FabrikamFiber.Web**, que contém o manifesto do serviço e as configurações para o novo serviço FabrikamFiber.Web.

   O contêiner agora está pronto para ser compilado e empacotado em um aplicativo do Service Fabric. Depois de compilar a imagem de contêiner no computador, você poderá enviá-la por push para qualquer registro de contêiner e efetuar pull dela em qualquer host para execução.

## <a name="run-the-containerized-application-locally"></a>Executar o aplicativo em contêineres localmente

Pressione **F5** para executar e depurar o aplicativo em um contêiner no cluster de desenvolvimento do Service Fabric. Clique em **Sim** se for exibida uma caixa de mensagem pedindo para conceder permissões de leitura e execução para o grupo “ServiceFabricAllowedUsers” para o seu diretório de projeto do Visual Studio.

Se a execução F5 gera uma exceção como a mostrada seguir, o IP correto não foi adicionado ao firewall do banco de dados do Azure.

```text
System.Data.SqlClient.SqlException
HResult=0x80131904
Message=Cannot open server 'fab-fiber-751718376' requested by the login. Client with IP address '123.456.789.012' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect.
Source=.Net SqlClient Data Provider
StackTrace:
<Cannot evaluate the exception stack trace>
```

Para adicionar o IP apropriado ao firewall do banco de dados do Azure, execute o comando a seguir.

```powershell
# The IP address of your development computer that accesses the SQL DB.
$clientIPNew = "<client IP from the Error Message>"

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClientNew" -StartIpAddress $clientIPNew -EndIpAddress $clientIPNew
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Agora que o aplicativo é executado localmente, comece a preparar a implantação no Azure.  As imagens de contêiner precisam ser armazenadas em um registro de contêiner.  Criar um [registro de contêiner do Azure](../container-registry/container-registry-intro.md) usando o script a seguir. O nome do registro de contêiner é visível por outras assinaturas do Azure, portanto ele deve ser exclusivo.
Antes de implantar o aplicativo no Azure, você pode enviar por push a imagem de contêiner para esse registro.  Quando o aplicativo for implantado no cluster no Azure, a imagem de contêiner é extraída desse registro.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Criar um cluster do Service Fabric no Azure

Aplicativos do Service Fabric são executados em um cluster, um conjunto conectado à rede de máquinas virtuais ou físicas.  Antes de implantar o aplicativo no Azure, crie um cluster do Service Fabric no Azure.

Você pode:

* Criar um cluster de teste no Visual Studio. Essa opção permite que você crie um cluster seguro diretamente no Visual Studio com suas configurações preferenciais.
* [Criar um cluster seguro a partir de um modelo](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

Este tutorial cria um cluster do Visual Studio, que é ideal para cenários de teste. Se você criar um cluster de alguma outra forma, ou usar um cluster existente, você pode copiar e colar o ponto de extremidade de conexão ou escolhê-lo em sua assinatura.

Antes de começar, abra FabrikamFiber.Web –> PackageRoot –> ServiceManifest.xml no Gerenciador de Soluções. Anote a porta do front-end da Web listada em **Ponto de extremidade**.

Ao criar o cluster:

1. Clique com o botão direito do mouse no projeto de aplicativo **Fabrikam.CallCenterApplication** no Gerenciador de Soluções e escolha **Publicar**.
2. Entre usando sua conta do Azure para que você possa ter acesso às assinaturas.
3. Abaixo da lista suspensa de **Ponto de Extremidade de Conexão**, selecione a opção **Criar Cluster...** .
4. Na caixa de diálogo **Criar cluster**, modifique as seguintes configurações:

    a. Especifique o nome do cluster no campo **Nome do Cluster**, bem como a assinatura e o local que você deseja usar. Anote o nome do seu grupo de recursos de cluster.

    b. Opcional: é possível modificar o número de nós. Por padrão, existem três nós, o mínimo necessário para testar cenários do Service Fabric.

    c. Selecione a guia **Certificado**. Nessa guia, digite uma senha a ser usada para proteger o certificado do cluster. Esse certificado ajuda a proteger o cluster. Você também pode modificar o caminho de onde deseja salvar o certificado. O Visual Studio também pode importar o certificado para você, já que essa é uma etapa necessária para publicar o aplicativo no cluster.

    >[!NOTE]
    >Anote o caminho da pasta em que esse certificado é importado. A próxima etapa após a criação do cluster é a importação desse certificado.

    d. Selecione a guia **Detalhes da VM**. Especifique a senha que você deseja usar para as VMs (máquinas virtuais) que compõem o cluster. O nome de usuário e a senha podem ser usados para se conectar remotamente às VMs. Você também deve selecionar um tamanho de máquina VM e pode alterar a imagem da VM, se necessário.

    > [!IMPORTANT]
    > Escolha um SKU que dê suporte a contêineres em execução. O sistema operacional Windows Server nos seus nós de cluster devem ser compatíveis com o sistema operacional Windows Server do seu contêiner. Para obter mais informações, consulte [Compatibilidade do sistema operacional contêiner e do sistema operacional do host do Windows Server](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Por padrão, este tutorial cria uma imagem do Docker com base no Windows Server 2016 LTSC. Contêineres com base nesta imagem serão executados em clusters criados com o Windows Server 2016 Datacenter com contêineres. No entanto, se você criar um cluster ou usar um cluster existente com base em uma versão diferente do Windows Server, você precisará alterar a imagem do SO na qual o contêiner se baseia. Abra o **dockerfile** no projeto **FabrikamFiber.Web**, comente qualquer instrução `FROM` existente baseada em uma versão anterior do Windows Server e adicione uma instrução `FROM` com base na marca da versão desejada da [página do DockerHub do Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore). Para obter informações adicionais sobre versões, linhas do tempo de suporte e controle de versão do Windows Server Core, confira [Informações de versão do Windows Server Core](/windows-server/get-started/windows-server-release-info). 

    e. Na guia **Avançado**, liste a porta do aplicativo que deverá ser aberta no balanceador de carga quando o cluster for implantado. Essa é a porta que você anotou antes de iniciar a criação do cluster. Você também pode adicionar uma chave existente do Application Insights a ser usada para direcionar os arquivos de log do aplicativo.

    f. Quando você terminar de modificar as configurações, selecione o botão **Criar**.

5. A criação levará alguns minutos para ser concluída; a janela de saída indica quando o cluster foi totalmente criado.

## <a name="install-the-imported-certificate"></a>Instalar o certificado importado

Instale o certificado importado como parte da etapa de criação do cluster, para a localização de armazenamento **Usuário Atual** e forneça a senha de chave privada fornecida.

Você pode confirmar a instalação abrindo **Gerenciar Certificados de Usuário** no painel de controle e verificando se o certificado está instalado em **Certificados – Usuário Atual** -> **Pessoal** -> **Certificados**. O certificado deve estar no formato *[Nome do Cluster]* . *[Localização do Cluster]* .cloudapp.azure.com, por exemplo, *fabrikamfibercallcenter.southcentralus.cloudapp.azure.com*. 

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Permita que o aplicativo em execução no Azure acesse o Banco de Dados SQL

Anteriormente, você criou uma regra de firewall do SQL para conceder acesso ao seu aplicativo em execução localmente.  Em seguida, você precisa habilitar o aplicativo em execução no Azure para acessar o Banco de Dados SQL.  Crie um [ponto de extremidade de serviço de rede virtual](../azure-sql/database/vnet-service-endpoint-rule-overview.md) para o cluster do Service Fabric e, em seguida, crie uma regra para permitir que esse ponto de extremidade acesse o Banco de Dados SQL. Especifique a variável de grupo de recursos de cluster que você observou ao criar o cluster.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure

Agora que o aplicativo está pronto, você poderá implantá-lo no cluster no Azure diretamente por meio do Visual Studio.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto de aplicativo **Fabrikam.CallCenterApplication** e selecione **Publicar**.  Em **Ponto de extremidade de Conexão**, selecione o ponto de extremidade do cluster que você criou anteriormente.  Em **Registro de Contêiner do Azure**, selecione o registro de contêiner que você criou anteriormente.  Clique em **Publicar** para implantar o aplicativo no cluster no Azure.

![aplicativo Publicar][publish-app]

Acompanhe o progresso da implantação na janela de saída. Quando o aplicativo for implantado, abra um navegador e digite o endereço do cluster e a porta do aplicativo. Por exemplo, `http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`.

![Captura de tela da home page do aplicativo Fabrikam Fiber CallCenter em execução no azure.com. A página mostra um painel com uma lista de chamadas de suporte.][fabrikam-web-page-deployed]

Se a página não for carregada ou falhar ao solicitar o certificado, tente abrir o caminho do Explorer (por exemplo, `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:19080/Explorer`) e selecione o certificado recém-instalado.

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Configurar a Integração e Implantação Contínuas (CI/CD) com o cluster do Service Fabric

Para saber como usar o Azure DevOps para configurar a implantação de aplicativos de CI/CD para um cluster do Service Fabric, confira [Tutorial: Implantar um aplicativo com CI/CD em um cluster do Service Fabric](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). O processo descrito no tutorial é o mesmo para este projeto (FabrikamFiber), basta ignorar o download do exemplo de Votação e substituir FabrikamFiber como o nome do repositório, em vez de Votação.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você tiver terminado, certifique-se de remover todos os recursos que você criou.  A maneira mais simples é remover os grupos de recursos que contêm o cluster do Service Fabric, o Banco de Dados SQL do Azure e o Registro de Contêiner do Azure.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Colocar um aplicativo existente em um contêiner usando o Visual Studio
> * Criar um banco de dados no Banco de Dados SQL do Azure
> * Criar um registro de contêiner do Azure
> * Implantar um aplicativo do Service Fabric no Azure

Na próxima parte do tutorial, saiba como [Implantar um aplicativo de contêiner com CI/CD para um cluster do Service Fabric](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
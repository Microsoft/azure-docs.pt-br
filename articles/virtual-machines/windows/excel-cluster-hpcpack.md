---
title: Cluster HPC Pack para Excel e SOA | Microsoft Docs
description: "Introdução à execução de cargas de trabalho SOA e Excel em larga escala em um cluster HPC Pack no Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 63babd94fdab15217cfb0757e4cd6efe458a628d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Introdução à execução de cargas de trabalho do Excel e SOA em um cluster HPC Pack no Azure
Este artigo mostra como implantar um cluster do Microsoft HPC Pack 2012 R2 em máquinas virtuais do Azure usando um modelo de início rápido do Azure ou, opcionalmente, um script de implantação do Azure PowerShell. O cluster usa as imagens de VM do Azure Marketplace projetadas para executar cargas de trabalho da arquitetura SOA ou do Microsoft Excel com o HPC Pack. Você pode usar o cluster para executar serviços de SOA e HPC do Excel de um computador cliente local. Os serviços do Excel HPC incluem descarregamento de pasta de trabalho do Excel e funções definidas pelo usuário do Excel ou UDFs.

> [!IMPORTANT] 
> Este artigo se baseia em recursos, modelos e scripts do HPC Pack 2012 R2. Atualmente, este cenário não tem suporte no HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Em um alto nível, o diagrama a seguir mostra o cluster HPC Pack criado.

![Cluster HPC com nós que executam cargas de trabalho do Excel][scenario]

## <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente** – você precisa de um computador cliente baseado no Windows para enviar trabalhos de exemplo do Excel e SOA ao cluster. Você também precisa de um computador Windows para executar o script de implantação de cluster do Azure PowerShell (caso escolha esse método de implantação).
* **Assinatura do Azure** – Se não tiver uma, você poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
* **Cota para núcleos** : talvez seja necessário aumentar a cota de núcleos, especialmente se você implantar vários nós de cluster com tamanhos de VM de vários núcleos. Se você estiver usando um modelo de início rápido do Azure, a cota de núcleos no Resource Manager será calculada por região do Azure. Nesse caso, talvez você precise aumentar a cota em uma região específica. Consulte [Assinatura do Azure e limite de serviços, cotas e restrições](../../azure-subscription-service-limits.md). Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.
* **Licença do Microsoft Office** – se você implantar nós de computação usando uma imagem de VM do Marketplace HPC Pack 2012 R2 com Microsoft Excel, uma versão de avaliação de 30 dias do Microsoft Excel Professional Plus 2013 será instalada. Após o período de avaliação, você precisa fornecer uma licença válida do Microsoft Office para ativar o Excel e continuar executando cargas de trabalho. Confira [Ativação do Excel](#excel-activation) mais adiante neste artigo. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Etapa 1. Configurar um cluster de HPC Pack no Azure
Mostramos duas opções para configurar o cluster do HPC Pack 2012 R2: primeiro, usando um modelo de início rápido do Azure e o portal do Azure; e segundo, usando um script de implantação do Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Opção 1. Usar um modelo de início rápido
Use um modelo de início rápido do Azure para implantar de maneira rápida um cluster do HPC Pack no portal do Azure. Ao abrir o modelo no portal, você obtém uma interface do usuário simples onde inserir as configurações para seu cluster. Siga estas etapas: 

> [!TIP]
> Se quiser, use um [modelo do Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) que cria um cluster semelhante, especificamente para cargas de trabalho do Excel. As etapas diferem ligeiramente das que se seguem.
> 
> 

1. Visite a [página Criar modelo de Cluster de HPC no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Se quiser, reveja as informações sobre o modelo e o código-fonte.
2. Clique em **Implantar no Azure** para iniciar uma implantação com o modelo no portal do Azure.
   
   ![Implantar o modelo no Azure][github]
3. No portal, siga estas etapas para especificar os parâmetros para o modelo de cluster HPC.
   
   a. Na página **Parâmetros** , insira ou modifique os valores para os parâmetros de modelo. Clique no ícone ao lado de cada configuração para obter informações de Ajuda. Os valores de exemplo são mostrados na tela a seguir. Este exemplo cria um cluster denominado *hpc01* no domínio *hpc.local*, que consiste em um nó do cabeçalho e dois nós de computação. Os nós de computação são criados com base em uma imagem de VM do HPC Pack que inclui o Microsoft Excel.
   
   ![Inserir parâmetros][parameters-new-portal]
   
   > [!NOTE]
   > A VM do nó de cabeçalho é criada automaticamente com base na [imagem mais recente do Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) do HPC Pack 2012 R2 no Windows Server 2012 R2. Atualmente, a imagem se baseia no HPC Pack 2012 R2 Update 3.
   > 
   > As VMs do nó de computação são criadas com base na imagem mais recente da família do nó de computação selecionada. Selecione a opção **ComputeNodeWithExcel** para a imagem do nó de computação HPC Pack mais recente que inclui uma versão de avaliação do Microsoft Excel Professional Plus 2013. Para implantar um cluster para sessões gerais de SOA ou para o descarregamento de UDF do Excel, escolha a opção **ComputeNode** (sem o Excel instalado).
   > 
   > 
   
   b. Selecione a assinatura.
   
   c. Crie um novo grupo de recursos para o cluster, como *hpc01RG*.
   
   d. Escolha um local para o grupo de recursos, como EUA Central.
   
   e. Na página **Termos legais** , analise os termos. Se concordar, clique em **Comprar**. Quando tiver terminado de definir os valores para o modelo, clique em **Criar**.
4. Quando a implantação for concluída (normalmente leva cerca de 30 minutos), exporte o arquivo de certificado de cluster do nó principal do cluster. Em uma etapa posterior, este certificado público será importado no computador cliente para fornecer a autenticação no servidor para a associação segura de HTTP.
   
   a. No portal do Azure, vá para o painel, selecione o nó principal e, em seguida, clique em **Conectar** na parte superior da página para se conectar usando a Área de Trabalho Remota.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Use os procedimentos padrão no Gerenciador de Certificados para exportar o certificado de nó principal (localizado em Cert:\LocalMachine\My) sem a chave privada. Neste exemplo, exporte *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Exportar o certificado][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Opção 2. Use o script de implantação do HPC Pack IaaS
O script de implantação do HPC Pack IaaS é outra forma versátil para implantar um cluster de HPC Pack. Ele cria um cluster no modelo de implantação clássica, enquanto o modelo usa o modelo de implantação do Azure Resource Manager. Além disso, o script é compatível com uma assinatura no serviço Azure Global ou Azure China.

**Pré-requisitos adicionais**

* **Azure PowerShell** - [Instale e configure o Azure PowerShell](/powershell/azure/overview) (versão 0.8.10 ou posterior) no computador cliente.
* **Script de implantação do Pacote HPC IaaS** : baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.5.0 ou posterior do script.

**Criar o arquivo de configuração**

 O script de implantação do HPC Pack de IaaS usa um arquivo de configuração XML como entrada que descreve a infraestrutura do cluster HPC. Para implantar um cluster que consiste em um nó principal e 18 nós de computação criados a partir da imagem do nó de computação que inclui o Microsoft Excel, substitua os valores para o seu ambiente no seguinte arquivo de configuração de exemplo. Para obter mais informações sobre o arquivo de configuração, consulte o arquivo Manual.rtf na pasta de script e [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notas sobre o arquivo de configuração**

* O **VMName** do nó do cabeçalho **DEVE** ser igual ao **ServiceName** ou os trabalhos SOA não funcionarão.
* Verifique se você especificou **EnableWebPortal** para que o certificado de nó principal seja gerado e exportado.
* O arquivo especifica um script do PowerShell pós-configuração PostConfig.ps1 que é executado no nó de cabeçalho. O script de exemplo a seguir configura a cadeia de conexão do armazenamento do Azure, remove a função do nó de computação do nó de cabeçalho e coloca todos os nós online quando são implantados. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Execute o script**

1. Abra o console do PowerShell no computador cliente como administrador.
2. Altere o diretório para a pasta de scripts (E:\IaaSClusterScript neste exemplo).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Para implantar o cluster HPC Pack, execute o comando a seguir. Este exemplo supõe que o arquivo de configuração esteja localizado em E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

O script de implantação do HPC Pack é executado por algum tempo. Uma das ações do script é exportar e baixar o certificado do cluster e salvá-lo na pasta Documentos do usuário atual no computador cliente. O script gera uma mensagem semelhante a mostrada a seguir. Em uma etapa posterior, você importa o certificado no repositório de certificados apropriado.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Etapa 2. Descarregaras pastas de trabalho do Excel e executar UDFs de um cliente local
### <a name="excel-activation"></a>Ativação do Excel
Ao usar a imagem da VM ComputeNodeWithExcel para cargas de trabalho de produção, você precisa fornecer uma chave de licença válida do Microsoft Office para ativar o Excel nos nós de computação. Caso contrário, a versão de avaliação do Excel expira após 30 dias e a execução das pastas de trabalho do Excel falhará com a COMException (0x800AC472). 

É possível rearmar o Excel por mais 30 dias de tempo de avaliação: faça logon no nó de cabeçalho e execute clusrun em `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` em todos os nós de computação do Excel por meio do Gerenciador de Cluster HPC. É possível rearmar, no máximo, duas vezes. Depois disso, é necessário fornecer uma chave de licença válida do Office.

O Office Professional Plus 2013 instalado na imagem de VM é uma edição de volume com uma GVLK (Chave de Licença de Volume Genérico). Você pode ativá-la por meio do KMS (Serviço de Gerenciamento de Chaves)/da AD-BA (Ativação Baseada no Active Directory) ou de uma MAK (Chave de Ativação Múltipla). 

    * Para usar o KMS/AD-BA, use um servidor KSM existente ou configure um novo usando o Pacote de Licença de Volume do Microsoft Office 2013. (Se você quiser, configure o servidor no nó do cabeçalho.) Em seguida, ative a chave do host KMS via Internet ou por telefone. Em seguida, execute o cluster `ospp.vbs` para definir o servidor KMS e a porta, e ative o Office em todos os nós de computação do Excel. 

    * Para usar a MAK, primeiramente execute o cluster `ospp.vbs` para inserir a chave e ative todos os nós de computação do Excel via Internet ou telefone. 

> [!NOTE]
> As chaves de produto de varejo do Office Professional Plus 2013 não podem ser usadas com essa imagem de VM. Se você tiver chaves válidas e mídia de instalação para as edições do Office ou do Excel diferentes desta edição de volume do Office Professional Plus 2013, será possível usá-las em seu lugar. Primeiro desinstale esta edição de volume e instale a edição que você tem. O nó de computação do Excel reinstalado pode ser capturado como uma imagem de VM personalizada para ser usada em uma implantação em escala.
> 
> 

### <a name="offload-excel-workbooks"></a>Descarregar pastas de trabalho do Excel
Siga estas etapas para descarregar uma pasta de trabalho do Excel para que ela seja executada no cluster HPC Pack no Azure. Para fazer isso, você deve ter o Excel 2010 ou 2013 já instalado no computador cliente.

1. Use um dos métodos da Etapa 1 para implantar um cluster HPC Pack com a imagem do nó de computação do Excel. Obter o arquivo de certificado (.cer) do cluster e o nome de usuário e a senha.
2. No computador cliente, importe o certificado de cluster em Cert:\CurrentUser\Root.
3. Verifique se o Excel está instalado. Crie um arquivo Excel.exe.config com o conteúdo a seguir na mesma pasta do Excel.exe no computador cliente. Essa etapa garante que o suplemento COM do Excel no HPC Pack 2012 R2 é carregado com êxito.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Configure o cliente para enviar trabalhos para o cluster HPC Pack. Uma opção é baixar a [instalação completa do HPC Pack 2012 R2 Atualização 3](http://www.microsoft.com/download/details.aspx?id=49922) e instalar o cliente do HPC Pack. Como alternativa, baixe e instale os [utilitários de cliente do HPC Pack 2012 R2 Atualização 3](https://www.microsoft.com/download/details.aspx?id=49923) e o Visual C++ 2010 redistribuível adequado para seu computador ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).
5. Neste exemplo, usamos uma pasta de trabalho do Excel de exemplo chamada ConvertiblePricing_Complete.xlsb. Você pode baixá-lo [aqui](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Copie a pasta de trabalho do Excel para uma pasta de trabalho, como D:\Excel\Run.
7. Abra a pasta de trabalho do Excel. Na faixa de opções **Desenvolver**, clique em **Suplementos COM** e confirme se o suplemento COM do HPC Pack do Excel foi carregado com êxito.
   
   ![Suplemento do Excel para o HPC Pack][addin]
8. Edite a macro do VBA HPCControlMacros no Excel, alterando as linhas comentadas, conforme mostrado no script a seguir. Substitua os valores apropriados para o seu ambiente.
   
   ![Macro do Excel para o HPC Pack][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
9. Copie a pasta de trabalho do Excel e, um diretório de upload como D:\Excel\Upload. Esse diretório é especificado na constante HPC_DependsFiles na macro VBA.
10. Para executar a pasta de trabalho no cluster do Azure, clique no botão **Cluster** na planilha.

### <a name="run-excel-udfs"></a>Executar UDFs do Excel
Para executar UDFs do Excel, siga as etapas de 1 a 3 acima para configurar o computador cliente. Para UDFs do Excel, não é necessário ter o aplicativo do Excel instalado em nós de computação. Portanto, ao criar os nós de computação do cluster, você poderá escolher uma imagem de nó de computação normal em vez da imagem do nó de computação com o Excel.

> [!NOTE]
> Há um limite de 34 caracteres no Excel 2010 e a caixa de diálogo do conector de cluster 2013. Use essa caixa de diálogo para especificar o cluster que executa as UDFs. Se o nome completo do cluster for maior (por exemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com), ele não caberá na caixa de diálogo. A solução alternativa é definir uma variável de todo o computador, como *CCP_IAASHN* com o valor do nome de cluster longo. Em seguida, digite *%CCP_IAASHN%* na caixa de diálogo como o nome do nó do cabeçalho do cluster. 
> 
> 

Depois que o cluster for implantado com êxito, continue com as etapas a seguir para executar um exemplo interno do UDF do Excel. Para UDFs personalizados do Excel, consulte estes [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para compilar os XLLs e implantá-los no cluster IaaS.

1. Abra uma nova pasta de trabalho do Excel. Na faixa de opções **Desenvolver**, clique em **Suplementos**. Em seguida, na caixa de diálogo, clique em **Procurar**, navegue até a pasta %CCP_HOME%Bin\XLL32 e selecione o exemplo ClusterUDF32.xll. Se o ClusterUDF32 não existir no computador cliente, copie-o da pasta %CCP_HOME%Bin\XLL32 no nó de cabeçalho.
   
   ![Selecionar o UDF][udf]
2. Clique em **Arquivo** > **Opções** > **Avançadas**. Em **Fórmulas**, marque a opção **Permitir que as funções XLL definidas pelo usuário executem um cluster de cálculo**. Então, clique em **Opções** e digite o nome completo do cluster em **Nome do nó do cabeçalho do cluster**. Conforme observado anteriormente, essa caixa de entrada é limitada a 34 caracteres, de modo que um nome de cluster longo pode não caber. Você pode usar variáveis de todo o computador aqui para nomes de cluster longos.
   
   ![Configurar o UDF][options]
3. Para executar o cálculo de UDF no cluster, clique na célula com o valor =XllGetComputerNameC() e pressione Enter. A função apenas recupera o nome do nó de computação no qual uma UDF é executada. Para a primeira execução, uma caixa de diálogo de credenciais solicita o nome de usuário e a senha para se conectar ao cluster IaaS.
   
   ![Executar UDF][run]
   
   Quando houver uma grande quantidade de células para calcular, pressione Alt-Shift-Ctrl + F9 para executar o cálculo em todas as células.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Etapa 3. Executar uma carga de trabalho SOA a partir de um cliente local
Para executar aplicativos gerais de SOA no cluster HPC Pack de IaaS, primeiro use um dos métodos da Etapa 1 para implantar o cluster. Nesse caso, especifique uma imagem genérica de nó de computação, pois você não precisará do Excel nos nós de computação. Depois, siga estas etapas:

1. Após receber o certificado do cluster, importe-o para o computador cliente em Cert:\CurrentUser\Root.
2. Instale o [SDK do HPC Pack 2012 R2 Atualização 3](http://www.microsoft.com/download/details.aspx?id=49921) e [Utilitários de cliente do HPC Pack 2012 R2 Atualização 3](https://www.microsoft.com/download/details.aspx?id=49923). Essas ferramentas permitem que você desenvolva e execute aplicativos cliente de SOA.
3. Baixe o [código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633)HelloWorldR2. Abra o HelloWorldR2.sln no Visual Studio 2010 ou 2012. (Atualmente, este exemplo não é compatível com versões mais recentes do Visual Studio.)
4. Crie o projeto EchoService primeiro. Em seguida, implante o serviço no cluster de IaaS da mesma maneira que você o implanta em um cluster local. Para obter etapas detalhadas, consulte o Leiame.doc no HelloWordR2. Modifique e crie o HelloWorldR2 e outros projetos, conforme descrito na seção a seguir, para gerar os aplicativos cliente de SOA que são executados em um cluster de IaaS do Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Usar associação Http com fila de armazenamento do Azure
Para usar a associação de Http com uma fila de armazenamento do Azure, faça algumas alterações no código de exemplo.

* Atualize o nome do cluster.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Se preferir, use o TransportScheme padrão em SessionStartInfo ou defina-o explicitamente como Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Use a associação padrão para o BrokerClient.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    Ou defina explicitamente usando o basicHttpBinding.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Opcionalmente, defina o sinalizador UseAzureQueue como verdadeiro na SessionStartInfo. Se não estiver configurado, ele será definido como true por padrão quando o nome do cluster tiver sufixos de domínio do Azure e o TransportScheme for Http.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Usar associação Http sem fila de armazenamento do Azure
Para usar a associação de HTTP sem uma fila de armazenamento do Azure, defina explicitamente o sinalizador UseAzureQueue como false em SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Usar associação NetTcp
Para usar a associação NetTcp, a configuração é semelhante a conectar-se a um cluster local. Você precisa abrir alguns pontos de extremidade na VM do nó de cabeçalho. Se você usou o script de implantação de IaaS do HPC Pack para criar o cluster, por exemplo, defina os pontos de extremidade no portal do Azure, conforme descrito a seguir.

1. Pare a VM.
2. Adicione as portas TCP 9090, 9087, 9091 e 9094 para a Sessão, Agente, trabalho do Agente e Serviços de dados, respectivamente
   
    ![Configurar pontos de extremidade][endpoint-new-portal]
3. Inicie a VM.

O aplicativo cliente SOA não requer alterações, exceto do nome principal para o nome completo do cluster IaaS.

## <a name="next-steps"></a>Próximas etapas
* Consulte [estes recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para saber mais sobre como executar cargas de trabalho do Excel com o HPC Pack.
* Consulte [Gerenciamento de serviços SOA no Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) para saber mais sobre como implantar e gerenciar serviços SOA com HPC Pack.

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png

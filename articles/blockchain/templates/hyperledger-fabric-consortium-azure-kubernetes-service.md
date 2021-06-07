---
title: Implantar o consórcio do Fabric do kubernetes no serviço do Azure
description: Como implantar e configurar uma rede do consórcio de malha de multirazão no serviço kubernetes do Azure
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 42d16adbc5e6396c8d5d38176ac7681c712f4555
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101096"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Implantar o consórcio do Fabric do kubernetes no serviço do Azure

Você pode usar o modelo de malha do AKS (serviço de kubernetes do Azure) para implantar e configurar uma rede do consórcio de malha de multirazão no Azure.

Depois de ler este artigo, você irá:

- Tenha um conhecimento funcional da malha de hiperrazãor e dos componentes que formam os blocos de construção de uma rede blockchain de malha de multirazão.
- Saiba como implantar e configurar uma rede do consórcio de malha de multirazão no serviço kubernetes do Azure para seus cenários de produção.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Escolher uma solução de Blockchain do Azure

Antes de optar por usar um modelo de solução, Compare seu cenário com os casos de uso comuns das opções disponíveis do Azure Blockchain:

Opção | Modelo de serviço | Caso de uso comum
-------|---------------|-----------------
Modelos de Solução | IaaS | Os modelos de solução são Azure Resource Manager modelos que você pode usar para provisionar uma topologia de rede blockchain totalmente configurada. Os modelos implantam e configuram Microsoft Azure serviços de computação, rede e armazenamento para um tipo de rede blockchain. Os modelos de solução são fornecidos sem um contrato de nível de serviço. Use o [Microsoft Q&uma página](/answers/topics/azure-blockchain-workbench.html) para obter suporte.
[Azure Blockchain Service](../service/overview.md) | PaaS | O Azure Blockchain Service Preview simplifica a formação, o gerenciamento e a governança de redes do consórcio Blockchain. Use o serviço Blockchain do Azure para soluções que exigem PaaS, gerenciamento de consórcio ou privacidade de contrato e transação.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | A versão prévia do Azure Blockchain Workbench é uma coleção de serviços e recursos do Azure que ajudam você a criar e implantar aplicativos Blockchain para compartilhar dados e processos de negócios com outras organizações. Use o Azure Blockchain Workbench para o protótipo de uma solução Blockchain ou uma prova de conceito para um aplicativo Blockchain. O Azure Blockchain Workbench é fornecido sem um contrato de nível de serviço. Use o [Microsoft Q&uma página](/answers/topics/azure-blockchain-workbench.html) para obter suporte.

## <a name="deploy-the-orderer-and-peer-organization"></a>Implantar o solicitante e a organização de mesmo nível

Para começar, você precisa de uma assinatura do Azure que possa suportar a implantação de várias máquinas virtuais e contas de armazenamento padrão. Se você não tiver uma assinatura do Azure, poderá [criar uma conta gratuita do Azure](https://azure.microsoft.com/free/).

Para começar a usar a implantação de componentes de rede do Fabric do multilimiar, vá para o [portal do Azure](https://portal.azure.com).

1. Selecione **criar um recurso**  >  **Blockchain** e, em seguida, pesquise a malha do Microsoft **Azure no serviço de kubernetes do Azure (versão prévia)**.

2. Insira os detalhes do projeto na guia **noções básicas** .

    ![Captura de tela que mostra a guia noções básicas.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Insira os seguintes detalhes:
    - **Assinatura**: escolha o nome da assinatura onde você deseja implantar os componentes de rede da malha do hiperrazão.
    - **Grupo de recursos**: Crie um novo grupo de recursos ou escolha um grupo de recursos vazio existente. O grupo de recursos manterá todos os recursos implantados como parte do modelo.
    - **Região**: escolha a região do Azure em que você deseja implantar o cluster do serviço kubernetes do Azure para os componentes da malha do hiperrazão. O modelo está disponível em todas as regiões em que AKS está disponível. Escolha uma região em que sua assinatura não está atingindo o limite de cota da máquina virtual (VM).
    - **Prefixo de recurso**: Insira um prefixo para a nomenclatura de recursos que são implantados. Ele deve ter menos de seis caracteres e a combinação de caracteres deve incluir números e letras.
4. Selecione a guia **configurações de malha** para definir os componentes de rede da malha do limiar que serão implantados.

    ![Captura de tela que mostra a guia Configurações de malha.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Insira os seguintes detalhes:
    - **Nome da organização**: Insira o nome da organização de malha do multirazão, que é necessária para várias operações do plano de dados. O nome da organização deve ser exclusivo por implantação.
    - **Componente de rede de malha**: escolha o **serviço de pedidos** ou **nós de mesmo nível**, com base no componente de rede blockchain que você deseja configurar.
    - **Número de nós**: os dois tipos de nós a seguir:
        - **Serviço de pedidos**: nós responsáveis pela ordenação de transações no razão. Selecione o número de nós para fornecer tolerância a falhas à rede. A contagem de nós de pedidos com suporte é 3, 5 e 7.
        - **Nós pares**: nós que hospedam razões e contratos inteligentes. Você pode escolher de 1 a 10 nós com base em seu requisito.
    - **Banco de dados de estado mundial do nó par**: bancos de dados de estado mundial para os nós pares. LevelDB é o banco de dados de estado padrão inserido no nó par. Ele armazena dados chaincode como pares de chave/valor simples e oferece suporte apenas a consultas de chave, intervalo de chaves e chaves compostas. CouchDB é um banco de dados de estado alternativo opcional que dá suporte a consultas avançadas quando os valores de dados chaincode são modelados como JSON. Esse campo é exibido quando você escolhe **nós par** na lista suspensa **componente de rede de malha** .
    - **Nome de usuário de AC de malha**: a autoridade de certificação de malha permite inicializar e iniciar um processo de servidor que hospeda a autoridade de certificação. Ele permite que você gerencie identidades e certificados. Cada cluster AKS implantado como parte do modelo terá um pod de AC de malha por padrão. Insira o nome de usuário que é usado para autenticação de AC de malha.
    - **Senha da AC de malha**: Insira a senha para autenticação de AC de malha.
    - **Confirmar senha**: Confirme a senha da AC da malha.
    - **Certificados**: se você quiser usar seus próprios certificados raiz para inicializar a AC de malha, escolha a opção **carregar certificado raiz para AC de malha** . Caso contrário, a AC de malha cria certificados autoassinados por padrão.
    - **Certificado raiz**: carregue o certificado raiz (chave pública) com o qual a AC de malha precisa ser inicializada. Há suporte para certificados de formato. PEM. Os certificados devem ser válidos e em um fuso horário UTC.
    - **Chave privada do certificado raiz**: carregue a chave privada do certificado raiz. Se você tiver um certificado. PEM, que tem uma chave pública e privada combinada, carregue-o aqui também.


6. Selecione a guia **configurações de cluster AKs** para definir a configuração de cluster do serviço kubernetes do Azure. O cluster AKS tem vários pods configurados para executar os componentes de rede da malha do limiar. Os recursos do Azure implantados são:

    - **Ferramentas de malha**: ferramentas que são responsáveis por configurar os componentes de malha de hiperrazãos.
    - Atestador **/peer pods**: os nós da rede de malha do limiar.
    - **Proxy**: um pod de proxy NGNIX por meio do qual os aplicativos cliente podem se comunicar com o cluster AKs.
    - **AC de malha**: o Pod que executa a AC de malha.
    - **PostgreSQL**: instância do banco de dados que mantém as identidades da AC da malha.
    - **Key Vault**: instância do serviço de Azure Key Vault que é implantado para salvar as credenciais de AC de malha e os certificados raiz fornecidos pelo cliente. O cofre é usado no caso de repetição de implantação de modelo para manipular a mecânica do modelo.
    - **Disco gerenciado**: instância do serviço de Managed disks do Azure que fornece um repositório persistente para o razão e para o banco de dados de estado mundial do nó par.
    - **IP público**: ponto de extremidade do cluster AKs implantado para comunicação com o cluster.

    Insira os seguintes detalhes: 

    ![Captura de tela que mostra a guia Configurações de cluster K S.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Nome do cluster kubernetes**: altere o nome do cluster AKs, se necessário. Esse campo é preenchido previamente com base no prefixo de recurso fornecido.
    - **Versão do kubernetes**: escolha a versão do kubernetes que será implantada no cluster. Com base na região que você selecionou na guia **noções básicas** , as versões disponíveis com suporte podem ser alteradas.
    - **Prefixo DNS**: Insira um prefixo de nome DNS (sistema de nomes de domínio) para o cluster AKs. Você usará o DNS para se conectar à API do kubernetes ao gerenciar contêineres depois de criar o cluster.
    - **Tamanho do nó**: para o tamanho do nó kubernetes, você pode escolher na lista de SKUs (unidades de manutenção de estoque) da VM disponíveis no Azure. Para obter um desempenho ideal, recomendamos o Standard DS3 v2.
    - **Contagem de nós**: Insira o número de nós kubernetes a serem implantados no cluster. É recomendável manter essa contagem de nós igual ou maior que o número de nós de malha de hiperrazãos especificados na guia **configurações de malha** .
    - **ID do cliente da entidade de serviço**: Insira a ID do cliente de uma entidade de serviço existente ou crie uma nova. Uma entidade de serviço é necessária para autenticação AKS. Consulte as [etapas para criar uma entidade de serviço](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal).
    - **Segredo do cliente da entidade de serviço**: Insira o segredo do cliente da entidade de serviço fornecida na ID do cliente para a entidade de serviço.
    - **Confirmar segredo do cliente**: Confirme o segredo do cliente para a entidade de serviço.
    - **Habilitar o monitoramento de contêiner**: escolha habilitar o monitoramento de AKs, que permite que os logs de AKs enviem por push para o espaço de trabalho log Analytics especificado.
    - **Log Analytics espaço de trabalho**: o espaço de trabalho log Analytics será preenchido com o espaço de trabalho padrão que é criado se o monitoramento estiver habilitado.

8. Selecione a guia **revisar e criar** . Esta etapa dispara a validação para os valores que você forneceu.
9. Depois da aprovação na validação, selecione **Criar**.

    A implantação geralmente leva de 10 a 12 minutos. O tempo pode variar dependendo do tamanho e do número de nós AKS especificados.
10. Após a implantação bem-sucedida, você será notificado por meio das notificações do Azure no canto superior direito.
11. Selecione **ir para o grupo de recursos** para verificar todos os recursos criados como parte da implantação do modelo. Todos os nomes de recursos serão iniciados com o prefixo fornecido na guia **noções básicas** .

## <a name="build-the-consortium"></a>Criar o consórcio

Para criar o blockchain Consortium depois de implantar o serviço de pedidos e os nós pares, execute as etapas a seguir em sequência. O azhlf (script de malha de hiperrazãor) do Azure ajuda você a configurar o consórcio, criar o canal e executar operações de chaincode.

> [!NOTE]
> O script de azhlf (malha de hiperrazãor) do Azure foi atualizado para fornecer mais funcionalidade. Se você quiser consultar o script antigo, consulte o [Leiame no GitHub](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Esse script é compatível com a malha de hiperrazãos no modelo do serviço kubernetes do Azure versão 2.0.0 e posterior. Para verificar a versão da implantação, siga as etapas em [solucionar problemas](#troubleshoot).

> [!NOTE]
> O script é fornecido para ajudar apenas nos cenários de demonstração, desenvolvimento e teste. O canal e o consórcio que esse script cria têm as políticas básicas de malha do hiperrazãor para simplificar os cenários de demonstração, desenvolvimento e teste. Para a configuração de produção, é recomendável atualizar as políticas de malha de acordo com o canal/Consortium, de acordo com as necessidades de conformidade da sua organização usando as APIs nativas da malha do programa.


Todos os comandos para executar o script de malha do Azure-limiar podem ser executados por meio da CLI (interface de linha de comando) do Azure bash. Você pode entrar no Azure Cloud Shell por meio da ![ opção malha do kubernetes no modelo do serviço de infraestrutura do Azure no ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) canto superior direito do portal do Azure. No prompt de comando, digite `bash` e selecione a tecla Enter para alternar para a CLI do bash ou selecione **bash** na barra de ferramentas Cloud Shell.

Consulte [Azure cloud Shell](../../cloud-shell/overview.md) para obter mais informações.

![Captura de tela que mostra os comandos no Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


A imagem a seguir mostra o processo passo a passo para criar um consórcio entre uma organização do solicitante e uma organização par. As seções a seguir mostram comandos detalhados para concluir essas etapas.

![Diagrama do processo para criar um consórcio.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Depois de concluir a configuração inicial, use o aplicativo cliente para obter as seguintes operações:  

- Gerenciamento de canal
- Gerenciamento de consórcio
- Gerenciamento de Chaincode

### <a name="download-client-application-files"></a>Baixar arquivos do aplicativo cliente

A primeira configuração é baixar os arquivos do aplicativo cliente. Execute os seguintes comandos para baixar todos os arquivos e pacotes necessários:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Esses comandos clonarão o código do aplicativo cliente da malha do Azure hiperlimiar para o repositório GitHub público, seguido do carregamento de todos os pacotes NPM dependentes. Após a execução bem-sucedida do comando, você poderá ver uma pasta node_modules no diretório atual. Todos os pacotes necessários são carregados na pasta node_modules.

### <a name="set-up-environment-variables"></a>Configurar variáveis de ambiente

Todas as variáveis de ambiente seguem a Convenção de nomenclatura de recursos do Azure.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Definir variáveis de ambiente para o cliente da organização do solicitante

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Definir variáveis de ambiente para o cliente da organização par

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Com base no número de organizações pares em seu Consórcio, talvez seja necessário repetir os comandos de pares e definir a variável de ambiente de acordo.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Definir variáveis de ambiente para uma conta de armazenamento do Azure

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Use os comandos a seguir para criar uma conta de armazenamento do Azure. Se você já tiver a conta de armazenamento do Azure, ignore esta etapa.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Use os comandos a seguir para criar um compartilhamento de arquivos na conta de armazenamento do Azure. Se você já tiver um compartilhamento de arquivos, ignore esta etapa.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Use os comandos a seguir para gerar uma cadeia de conexão para um compartilhamento de arquivos do Azure.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importar um perfil de conexão da organização, a identidade do usuário administrador e o MSP

Use os comandos a seguir para buscar o perfil de conexão da organização, a identidade do usuário administrador e o provedor de serviços gerenciado (MSP) do cluster do serviço kubernetes do Azure e armazenar essas identidades no armazenamento local do aplicativo cliente. Um exemplo de um repositório local é o diretório *azhlfTool/* Stores.

Para a organização do solicitante:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Para a organização par:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Criar um canal

No cliente da organização do solicitante, use o comando a seguir para criar um canal que contenha apenas a organização do solicitante.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Adicionar uma organização par para o gerenciamento de consórcio

>[!NOTE]
> Antes de começar com qualquer operação de consórcio, verifique se você concluiu a configuração inicial do aplicativo cliente.  

Execute os seguintes comandos na ordem determinada para adicionar uma organização par em um canal e Consórcio: 

1.  No cliente da organização par, carregue o MSP da organização par no armazenamento do Azure.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  No cliente da organização do solicitante, baixe o MSP da organização par do armazenamento do Azure. Em seguida, emita o comando para adicionar a organização par no canal e consórcio.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  No cliente da organização do solicitante, carregue o perfil de conexão do solicitante no armazenamento do Azure para que a organização par possa se conectar aos nós do solicitante usando esse perfil de conexão.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  No cliente da organização par, baixe o perfil de conexão do solicitante do armazenamento do Azure. Em seguida, execute o comando para adicionar nós de mesmo nível no canal.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Da mesma forma, para adicionar mais organizações pares no canal, atualize as variáveis de ambiente par de acordo com a organização de pares necessária e refaça as etapas 1 a 4.

### <a name="set-anchor-peers"></a>Definir pontos de ancoragem

No cliente da organização par, execute o comando para definir os pares âncora para a organização par no canal especificado.

>[!NOTE]
> Antes de executar esse comando, verifique se a organização par está adicionada ao canal usando os comandos de gerenciamento do consórcio.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` é uma lista separada por espaços de nós pares a serem definidos como um par âncora. Por exemplo:

  - Defina `<anchorPeersList>` como `"peer1"` se você quiser definir apenas o nó ponto1 como um par âncora.
  - Defina `<anchorPeersList>` como `"peer1" "peer3"` se desejar definir os nós ponto1 e Ponto3 como pares âncora.

## <a name="chaincode-management-commands"></a>Comandos de gerenciamento do Chaincode

>[!NOTE]
> Antes de começar com qualquer operação do chaincode, verifique se você fez a configuração inicial do aplicativo cliente.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Definir as variáveis de ambiente específicas do chaincode

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Instalar o chaincode  

Execute o comando a seguir para instalar o chaincode na organização par.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
O comando instalará o chaincode em todos os nós pares da organização par definida na `ORGNAME` variável de ambiente. Se duas ou mais organizações pares estiverem em seu canal e você quiser instalar o chaincode em todos eles, execute esse comando separadamente para cada organização de mesmo nível.  

Siga estas etapas:  

1.  Defina `ORGNAME` e de `USER_IDENTITY` acordo com `peerOrg1` e execute o `./azhlf chaincode install` comando.  
2.  Defina `ORGNAME` e de `USER_IDENTITY` acordo com `peerOrg2` e execute o `./azhlf chaincode install` comando.  

### <a name="instantiate-chaincode"></a>Criar instância de chaincode  

No aplicativo cliente par, execute o seguinte comando para criar uma instância de chaincode no canal.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Passe o nome da função de instanciação e a lista de argumentos separados por espaços no `<instantiateFunc>` e, `<instantiateFuncArgs>` respectivamente. Por exemplo, para instanciar chaincode_example02. go chaincode, defina `<instantiateFunc>` como `init` e `<instantiateFuncArgs>` como `"a" "2000" "b" "1000"` .

Você também pode passar o arquivo JSON de configuração da coleção usando o `--collections-config` sinalizador. Ou defina os argumentos transitórios usando o `-t` sinalizador ao criar uma instância de chaincode usada para transações privadas.

Por exemplo:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

A `<collectionConfigJSONFilePath>` parte é o caminho para o arquivo JSON que contém as coleções definidas para a instanciação de chaincode de dados privados. Você pode encontrar um arquivo JSON de configuração da coleção de exemplo em relação ao diretório *azhlfTool* no seguinte caminho: `./samples/chaincode/src/private_marbles/collections_config.json` .
Passe `<transientArgs>` como um JSON válido no formato de cadeia de caracteres. Escapar de qualquer caractere especial. Por exemplo: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Execute o comando uma vez em qualquer organização de mesmo nível no canal. Depois que a transação for enviada com êxito para o solicitante, o solicitante distribuirá essa transação para todas as organizações pares no canal. Em seguida, Chaincode é instanciado em todos os nós de mesmo nível em todas as organizações pares no canal.  

### <a name="invoke-chaincode"></a>Invocar chaincode  

No cliente da organização par, execute o seguinte comando para invocar a função chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Passe o nome da função Invoke e a lista de argumentos separados por espaços no  `<invokeFunction>`   e,  `<invokeFuncArgs>`   respectivamente. Continuando com o exemplo de chaincode_example02. go chaincode, para executar uma operação Invoke, defina  `<invokeFunction>`   como  `invoke`   e  `<invokeFuncArgs>`   como `"a" "b" "10"` .  

>[!NOTE]
> Execute o comando uma vez em qualquer organização de mesmo nível no canal. Depois que a transação for enviada com êxito para o solicitante, o solicitante distribuirá essa transação para todas as organizações pares no canal. O estado mundial é então atualizado em todos os nós de mesmo nível de todas as organizações pares no canal.  


### <a name="query-chaincode"></a>Chaincode de consulta  

Execute o seguinte comando para consultar o chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Os colegas de endosso são os pontos em que o chaincode está instalado e é chamado para execução de transações. Você deve definir `<endorsingPeers>` para conter nomes de nó de par da organização par atual. Liste os pontos de endosso para uma determinada combinação de chaincode e canal, separados por espaços. Por exemplo: `-p "peer1" "peer3"`.

Se você estiver usando *azhlfTool* para instalar o chaincode, passe quaisquer nomes de nó de par como um valor para o argumento de ponto de endosso. O Chaincode é instalado em todos os nós de mesmo nível para essa organização. 

Passe o nome da função de consulta e a lista de argumentos separados por espaços no  `<queryFunction>`   e,  `<queryFuncArgs>`   respectivamente. Novamente, levando chaincode_example02. go chaincode como uma referência, para consultar o valor de "a" no estado mundial, defina  `<queryFunction>`   como  `query` e  `<queryArgs>` como `"a"` .  

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="find-deployed-version"></a>Localizar versão implantada

Execute os comandos a seguir para localizar a versão de sua implantação de modelo. Defina variáveis de ambiente de acordo com o grupo de recursos em que o modelo foi implantado.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Versão anterior do patch

Se você estiver enfrentando problemas com a execução do chaincode em todas as implantações da versão do modelo abaixo do v 3.0.0, siga as etapas abaixo para corrigir seus nós de mesmo nível com uma correção.

Baixe o script de implantação de mesmo nível.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Execute o script usando o comando a seguir substituindo os parâmetros de seu par.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Aguarde até que todos os nós de mesmo nível sejam corrigidos. Você sempre pode verificar o status de seus nós de mesmo nível, em uma instância diferente do shell usando o comando a seguir.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Suporte e comentários

Para se manter atualizado sobre as ofertas de serviço do blockchain e informações da equipe de engenharia do Blockchain do Azure, visite o [blog do Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/).

Para fornecer comentários sobre o produto ou solicitar novos recursos, poste um vote em uma ideia usando o [fórum de comentários do Azure para blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Suporte da comunidade

Entre em contato com engenheiros da Microsoft e especialistas da Comunidade do Azure Blockchain:

- [Microsoft Q&uma página](/answers/topics/azure-blockchain-workbench.html) 
   
  O suporte de engenharia para modelos blockchain é limitado a problemas de implantação.
- [Comunidade de tecnologia da Microsoft](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

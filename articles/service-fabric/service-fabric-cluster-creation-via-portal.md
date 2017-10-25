---
title: Criar um cluster do Service Fabric no portal do Azure | Microsoft Docs
description: Este artigo descreve como configurar um cluster seguro do Service Fabric no Azure usando o portal do Azure e o Cofre de Chaves do Azure.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: chackdan
ms.openlocfilehash: 3dd4f3494bb9ed70549f41e22c58666cada8da07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Criar um cluster do Service Fabric no usando o portal do Azure
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos do Azure](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Este é um guia passo a passo que orienta você pelas etapas de configuração de um cluster do Service Fabric seguro no Azure usando o portal do Azure. Este guia apresenta as seguintes etapas:

* Configure o Cofre de Chaves para gerenciar chaves para segurança de cluster.
* Crie um cluster seguro no Azure por meio do portal do Azure.
* Autentique os administradores que usam certificados.

> [!NOTE]
> Para obter opções de segurança mais avançadas, como autenticação de usuário com o Azure Active Directory e configurar certificados para a segurança de aplicativo, [crie o cluster usando o Azure Resource Manager][create-cluster-arm].
> 
> 

Um cluster seguro é um cluster que impede o acesso não autorizado às operações de gerenciamento, que inclui implantar, atualizar e excluir aplicativos, serviços e os dados que eles contêm. Um cluster não seguro é um cluster ao qual qualquer pessoa pode se conectar a qualquer momento e realizar operações de gerenciamento. Embora seja possível criar um cluster não seguro, é **altamente recomendável criar um cluster seguro**. Um cluster não seguro **não pode ser protegido posteriormente** - um novo cluster deverá ser criado.

Os conceitos são os mesmos para a criação de clusters seguros, se os clusters são do Linux ou do Windows. Para obter mais informações e scripts auxiliares para criar clusters do Linux seguras, consulte [Criando clusters seguros no Linux](service-fabric-cluster-creation-via-arm.md#secure-linux-clusters). Os parâmetros obtidos pelo script auxiliar fornecido podem ser inseridos diretamente no portal, conforme descrito na seção [Criar um cluster no portal do Azure](#create-cluster-portal).

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Este guia usa o [Azure PowerShell][azure-powershell]. Ao iniciar uma nova sessão do PowerShell, faça logon em sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.

Faça logon na sua conta do Azure:

```powershell
Login-AzureRmAccount
```

Selecione sua assinatura:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurar o Cofre de Chaves
Esta parte do guia mostra a criação de um Cofre de Chaves para um cluster do Service Fabric no Azure e para aplicativos do Service Fabric. Para obter um guia completo sobre o Key Vault, consulte o [guia de introdução ao Key Vault][key-vault-get-started].

O Service Fabric usa certificados x. 509 para proteger um cluster. O Cofre de Chaves do Azure é usado para gerenciar certificados para clusters do Service Fabric no Azure. Quando um cluster é implantado no Azure, o provedor de recursos do Azure responsável pela criação de clusters do Service Fabric recebe certificados do Cofre de Chaves e os instala nas VMs do cluster.

O diagrama a seguir ilustra o relacionamento entre o Cofre de Chaves, um cluster do Service Fabric e o provedor de recursos do Azure que usa certificados armazenados no Cofre de Chaves quando ele cria um cluster:

![Instalação do certificado][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
A primeira etapa é criar um novo grupo de recursos especificamente para o Cofre de Chaves. Colocar o Cofre de Chaves em seu próprio grupo de recursos é recomendado para que você possa remover grupos de recursos de computação e armazenamento, como o grupo de recursos com o cluster do Service Fabric - sem perder suas chaves e segredos. O grupo de recursos com o Cofre de Chaves deve estar na mesma região que o cluster que está sendo usado.

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Criar Cofre da Chave
Crie um Cofre de Chaves no novo grupo de recursos. O Cofre de Chaves **deve estar habilitado para implantação** para permitir que o provedor de recursos do Service Fabric obtenha certificados ele e os instale em nós de cluster:

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

Se você tiver um Cofre de Chaves existente, poderá habilitá-lo para implantação usando a CLI do Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


## <a name="add-certificates-to-key-vault"></a>Adicionar certificados ao Cofre de Chaves
Os certificados são usados no Service Fabric para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e de seus aplicativos. Para obter mais informações sobre como os certificados são usados no Service Fabric, consulte [Cenários de segurança do cluster do Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e de servidor (necessário)
Esse certificado é necessário para proteger um cluster e impedir o acesso não autorizado a ele. Ele fornece segurança de cluster de duas maneiras:

* **Autenticação do cluster:** autentica a comunicação de nó para nó para a federação de cluster. Somente os nós que podem provar sua identidade com esse certificado podem ingressar no cluster.
* **Autenticação de servidor:** autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o gerenciamento do cliente saiba que está se comunicando com o cluster real. Esse certificado também fornece SSL para a API de gerenciamento de HTTPS e para o Service Fabric Explorer sobre HTTPS.

Para servir a essas finalidades, o certificado deverá atender a estes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O nome de assunto do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric. Isso é necessário para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio `.cloudapp.azure.com` . Adquira um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

### <a name="client-authentication-certificates"></a>Certificados de autenticação de cliente
Os certificados de cliente adicionais autenticam os administradores para tarefas de gerenciamento de cluster. O Service Fabric tem dois níveis de acesso: **administrador** e **usuário somente leitura**. No mínimo, um único certificado para acesso administrativo deve ser usado. Para acesso de nível de usuário adicional, deve ser fornecido um certificado diferente. Para obter mais informações sobre as funções de acesso, consulte [Controle de acesso baseado em função para clientes do Service Fabric][service-fabric-cluster-security-roles].

Você não precisa carrear os certificados de autenticação do cliente no Cofre de Chaves para trabalhar com o Service Fabric. Esses certificados só precisam ser fornecido para os usuários autorizados para gerenciamento de cluster. 

> [!NOTE]
> O Azure Active Directory é a maneira recomendada para autenticar clientes para operações de gerenciamento de cluster. Para usar o Azure Active Directory, é necessário [criar um cluster usando o Azure Resource Manager][create-cluster-arm].
> 
> 

### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcionais)
Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança do aplicativo. Antes de criar o cluster, considere os cenários de segurança de aplicativos que exigem um certificado a ser instalado em nós, como:

* Criptografia e descriptografia de valores de configuração de aplicativo
* Criptografia de dados entre nós durante a replicação 

Os certificados do aplicativo não podem ser configurados durante a criação de um cluster por meio do portal do Azure. Para configurar certificados de aplicativo durante a instalação do cluster, é necessário [criar um cluster usando o Azure Resource Manager][create-cluster-arm]. Você também pode adicionar certificados do aplicativo ao cluster após ele ter sido criado.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formatação de certificados para uso do provedor de recursos do Azure
Os arquivos de chave privada (.pfx) podem ser adicionados e usados diretamente por meio do Cofre de Chaves. No entanto, o provedor de recursos do Azure requer chaves para ser armazenado em um formato JSON especial que inclui o .pfx como uma cadeia de caracteres codificada em base 64 e a senha da chave privada. Para acomodar esses requisitos, as chaves deverão ser colocadas em uma cadeia de caracteres JSON e então armazenadas como *segredos* no Cofre de Chaves.

Para facilitar esse processo, um módulo do PowerShell está [disponível no GitHub][service-fabric-rp-helpers]. Siga estas etapas para usar o módulo:

1. Baixe todo o conteúdo do repositório em um diretório local. 
2. Importe o módulo na janela do PowerShell:

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

O comando `Invoke-AddCertToKeyVault` neste módulo do PowerShell formata automaticamente uma chave privada do certificado em uma cadeia de caracteres JSON e a carrega no Cofre de Chaves. Use-o para adicionar o certificado do cluster e todos os certificados adicionais de aplicativos para o Cofre de Chaves. Repita esta etapa para todos os certificados adicionais que deseja instalar em seu cluster.

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Esses são todos os pré-requisitos do Cofre de Chaves para configurar um modelo do Resource Manager de cluster do Service Fabric que instala certificados para autenticação de nó, autenticação e segurança de ponto de extremidade de gerenciamento e recursos de segurança adicionais de aplicativos que usam certificados x.509. Neste ponto, agora você deve ter a seguinte configuração no Azure:

* Grupo de recursos do Cofre de Chaves
  * Cofre da Chave
    * Certificado de autenticação de servidor de cluster

</a "create-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Criar um cluster no portal do Azure
### <a name="search-for-the-service-fabric-cluster-resource"></a>Pesquise o recurso de cluster do Service Fabric
![pesquisa pelo modelo de cluster do Service Fabric no portal do Azure.][SearchforServiceFabricClusterTemplate]

1. Entre no [Portal do Azure][azure-portal].
2. Clique em **+ Novo** para adicionar um novo modelo de recurso. Procure o modelo Cluster do Service Fabric no **Marketplace** em **Tudo**.
3. Selecione **Cluster do Service Fabric** na lista.
4. Navegue até a folha **Cluster do Service Fabric** e clique em **Criar**,
5. A folha **Criar cluster do Service Fabric** tem as quatro etapas a seguir.

#### <a name="1-basics"></a>1. Noções básicas
![Captura de tela da criação de um novo grupo de recursos.][CreateRG]

Na folha Básico, você precisa fornecer os detalhes básicos do seu cluster.

1. Insira o nome do seu cluster.
2. Insira um **nome de usuário** e uma **senha** para a Área de Trabalho Remota para as VMs.
3. Selecione a **Assinatura** desejada para a implantação do cluster, especialmente se você tiver várias assinaturas.
4. Crie um **novo grupo de recursos**. É melhor dar a ele o mesmo nome do cluster, pois ajuda a encontrá-los mais tarde, especialmente quando você estiver tentando fazer alterações em sua implantação ou excluir o cluster.
   
   > [!NOTE]
   > Embora você possa optar por usar um grupo de recursos existente, é uma boa prática criar um novo grupo de recursos. Isso facilita a exclusão dos clusters que não são necessários.
   > 
   > 
5. Selecione a **região** ma qual você deseja criar o cluster. Você deve usar a mesma região em que está o Cofre de Chaves.

#### <a name="2-cluster-configuration"></a>2. Configuração do cluster
![Criar um tipo de nó][CreateNodeType]

Configure os nós de cluster. Os tipos de nó definem os tamanhos e o número de VMs e suas propriedades. O cluster pode ter mais de um tipo de nó, mas o tipo de nó primário (o primeiro que você define no portal) deve ter pelo menos cinco VMs. Esse é o tipo de nó onde os serviços do sistema do Service Fabric são colocados. Não configure as **Propriedades de Posicionamento**, pois uma propriedade de posicionamento padrão de "NodeTypeName" é adicionada automaticamente.

> [!NOTE]
> Um cenário comum para vários tipos de nó é um aplicativo que contém um serviço front-end e um serviço back-end. Você quer colocar o serviço front-end em VMs menores (tamanhos como D2) com portas abertas para a Internet, mas você quer colocar o serviço de back-end em VMs maiores (com tamanhos como D4, D6, D15 e assim por diante) com portas não são voltadas para a Internet abertas.
> 
> 

1. Escolha um nome para o tipo de nó (um a 12 caracteres contendo somente letras e números).
2. O **tamanho** mínimo das VMs para o tipo de nó primário é determinado pela camada de **durabilidade** que você escolhe para o cluster. O padrão para a camada de durabilidade é Bronze. Para obter mais informações sobre durabilidade, consulte [como escolher a durabilidade e confiabilidade do cluster do Service Fabric][service-fabric-cluster-capacity].
3. Selecione o tamanho da VM e o tipo de preços. As VMs da série D têm unidades SSD e são altamente recomendadas para aplicativos com monitoração de estado. Não use nenhuma SKU de VM com núcleos parciais ou que tenham menos de 7 GB de capacidade em disco disponível. 
4. O **número** mínimo de VMs para o tipo de nó primário é determinado pela camada de **confiabilidade** que você escolhe. O padrão para a camada de confiabilidade é Prata. Para obter mais informações sobre confiabilidade, consulte [como escolher a durabilidade e confiabilidade do cluster do Service Fabric][service-fabric-cluster-capacity].
5. Escolha o número de VMs para o tipo de nó. Você pode escalar ou reduzir verticalmente o número de VMs em um tipo de nó posteriormente, mas no tipo de nó primário, o mínimo é determinado pelo nível de confiabilidade que você escolheu. Os outros tipos de nó podem ter um mínimo de 1 VM.
6. Configure pontos de extremidade personalizados. Este campo permite que você insira uma lista separada por vírgulas de portas que você deseja expor por meio do Azure Load Balancer para a Internet pública para seus aplicativos. Por exemplo, se você planeja implantar um aplicativo web para o cluster, insira "80" aqui para permitir o tráfego na porta 80 em seu cluster. Para obter mais informações sobre pontos de extremidade, consulte [Comunicando-se com aplicativos][service-fabric-connect-and-communicate-with-services]
7. Configure o **diagnóstico**do cluster. Por padrão, os diagnósticos são habilitados no cluster para ajudar na solução de problemas. Se quiser desabilitar o diagnóstico, alterne o **Status** para **Desligado**. **Não** é recomendável desligar o diagnóstico.
8. Selecione o modo de atualização do Fabric para o qual você deseja definir o cluster. Selecione **Automático**, se você desejar que o sistema automaticamente obtenha a versão mais recente e tente atualizar o cluster para ela. Defina o modo como **Manual**, se você desejar escolher uma versão com suporte.

> [!NOTE]
> Damos suporte somente para clusters que executam versões com suporte do Service Fabric. Selecionando o modo **Manual** , você está assumindo a responsabilidade de atualizar seu cluster para uma versão com suporte. Para obter mais detalhes sobre o modo de atualização do Fabric, consulte o documento [Atualizar um cluster do Service Fabric.][service-fabric-cluster-upgrade]
> 
> 

#### <a name="3-security"></a>3. Segurança
![Captura de tela das configurações de segurança no Portal do Azure][SecurityConfigs]

A etapa final é fornecer informações de certificado para proteger o cluster usando o Cofre de Chaves e o certificado informações criado anteriormente.

* Preencha os campos de certificado principal com a saída obtida do carregamento do **certificado do cluster** para o Cofre de Chaves usando o comando `Invoke-AddCertToKeyVault` do PowerShell.

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* Marque a caixa **Definir configurações avançadas** para inserir certificados de cliente para **cliente administrativo** e **cliente somente leitura**. Nesses campos, insira a impressão digital do seu certificado de cliente do administrador e a impressão digital do seu certificado de cliente do usuário somente leitura, se aplicável. Quando os administradores tentam se conectar ao cluster, eles só receberão acesso se tiverem um certificado com uma impressão digital que corresponda aos valores da impressão digital inseridos aqui.  

#### <a name="4-summary"></a>4. Resumo

Para concluir a criação do cluster, clique em **Resumo** para ver as configurações que você forneceu ou baixe o modelo do Azure Resource Manager que será usado para implantar o cluster. Depois de ter fornecido as configurações obrigatórias, o botão **OK** fica verde e você pode começar o processo de criação do cluster clicando nele.

Você pode ver o progresso da criação nas notificações. (Clique no ícone de "Sino" próximo à barra de status no canto superior direito da tela). Se você clicou em **Fixar no Quadro Inicial** durante a criação do cluster, verá **Implantando o Cluster do Service Fabric** fixado na **Tela Inicial**.

### <a name="view-your-cluster-status"></a>Exibir o status do cluster
![Captura de tela dos detalhes do cluster no painel de controle.][ClusterDashboard]

Depois que o cluster for criado, você poderá inspecioná-lo no portal:

1. Vá para **Procurar** e clique em **Clusters do Service Fabric**.
2. Localize o cluster e clique nele.
3. Agora você pode ver os detalhes do cluster no painel, inclusive o ponto de extremidade público do cluster e um link para o Service Fabric Explorer.

A seção **Monitor do Nó** na folha do painel do cluster indica o número de VMs íntegras e não íntegras. Encontre mais detalhes sobre a integridade do cluster em [Introdução ao monitoramento da integridade do Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Os clusters de Service Fabric exigem um determinado número de nós esteja sempre ativo para manter a disponibilidade e preservar o estado – conhecido como "manter o quórum". Portanto, normalmente não é seguro desligar todos os computadores no cluster, a menos que você tenha primeiro feito um [backup completo do estado][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conectar remotamente a uma instância do Conjunto de Escala de Máquinas Virtuais ou a um nó de cluster
Cada um dos NodeTypes que você especifica no cluster resulta na configuração de um Conjunto de dimensionamento de máquinas virtuais. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um cluster seguro usando certificados para autenticação de gerenciamento. Em seguida, [conecte-se ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).  Além disso, saiba mais sobre as [Azure Service Fabric support options](service-fabric-support.md) (Opções de suporte do Service Fabric).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png

---
title: Tutorial - Criar um cluster do Red Hat OpenShift no Azure|Microsoft Docs
description: Saiba como criar um cluster do Red Hat OpenShift no Microsoft Azure usando a CLI do Azure
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/14/2019
ms.openlocfilehash: 9fd37a8343858f44719fe4422b3b9994db42f8af
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672476"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Tutorial: Criar um cluster do Red Hat OpenShift no Azure

Este tutorial é a primeira parte de uma série. Você aprenderá a criar um cluster do Red Hat OpenShift no Microsoft Azure usando a CLI do Azure, dimensioná-lo e, em seguida, excluí-lo para limpar os recursos.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar um cluster do Red Hat OpenShift no Azure

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um cluster do Red Hat OpenShift no Azure
> * [Dimensionar um cluster do Red Hat OpenShift no Azure](tutorial-scale-cluster.md)
> * [Excluir um cluster do Red Hat OpenShift no Azure](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Este tutorial exige a CLI do Azure versão 2.0.65 ou posterior.
>    
> Antes que possa usar o Red Hat OpenShift no Azure, você precisará comprar um mínimo de 4 nós de aplicativo reservados do Red Hat OpenShift no Azure, conforme descrito em [Configurar o ambiente de desenvolvimento do Red Hat OpenShift no Azure](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Antes de começar este tutorial:

Certifique-se de [configurar o ambiente de desenvolvimento](howto-setup-environment.md), o que inclui:
- Como instalar a CLI mais recente (versão 2.0.65 ou posterior)
- Como criar um locatário se você ainda não tiver um
- Criar um objeto de Aplicativo do Azure se você ainda não tiver um
- Criação de um grupo de segurança
- Como criar um usuário do Active Directory para entrar no cluster.

## <a name="step-1-sign-in-to-azure"></a>Etapa 1: Entrar no Azure

Se você estiver executando a CLI do Azure localmente, abra um shell de comando de Bash e execute `az login` para entrar no Azure.

```bash
az login
```

 Caso tenha acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Etapa 2: Criar um cluster do Red Hat OpenShift no Azure

Na janela Comando do Bash, defina as variáveis a seguir:

> [!IMPORTANT]
> Escolha um nome exclusivo e todo em minúsculas para seu cluster, caso contrário a criação do cluster falhará.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Escolha uma localização para criar o cluster. Para obter uma lista de regiões do Azure que dão suporte ao OpenShift no Azure, consulte [Regiões com suporte](supported-resources.md#azure-regions). Por exemplo: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Defina `APPID` para o valor que você salvou na etapa 5 em [Criar um novo registro de aplicativo do Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Defina 'GROUPID' para o valor que você salvou na etapa 10 em [Criar um novo grupo de segurança do Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Defina `SECRET` para o valor que você salvou na etapa 8 em [Criar um segredo do cliente](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Defina `TENANT` para o valor da ID de locatário que você salvou na etapa 7 em [Criar um movo locatário](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Crie um grupo de recursos para o cluster. Execute o seguinte comando no mesmo shell de Bash que você usou para definir as variáveis acima:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcional: Conectar a rede virtual do cluster a uma rede virtual existente

Se você não precisar conectar a VNET (rede virtual) do cluster criado a uma VNET existente por meio do emparelhamento, ignore esta etapa.

Se emparelhar a uma rede fora da assinatura padrão, na assinatura, você também precisará registrar o provedor Microsoft.ContainerService. Para fazer isso, execute o comando abaixo nessa assinatura. Caso contrário, se estiver realizando o emparelhamento da VNET na mesma assinatura, você pode ignorar a etapa de registro. 

`az provider register -n Microsoft.ContainerService --wait`

Primeiro, obtenha o identificador da VNET existente. O identificador será do formato: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Se você não souber o nome da rede ou o grupo de recursos ao qual pertence a rede virtual existente, vá para a [Folha redes virtuais](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) e clique na sua rede virtual. A página da Rede virtual será exibida e listará o nome da rede e o grupo de recursos que ela pertence.

Defina uma variável VNET_ID usando o seguinte comando da CLI em um shell de BASH:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Por exemplo: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Criar o cluster

Agora você está pronto para criar um cluster. Os comandos a seguir criarão o cluster no locatário do Azure AD especificado, especificarão o objeto de aplicativo do Azure AD e o segredo a usar como uma entidade de segurança, bem como o grupo de segurança que contém os membros que têm acesso administrativo ao cluster.

Se você **não** estiver emparelhando o cluster a uma rede virtual, use o seguinte comando:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Se você **estiver** emparelhando o cluster a uma rede virtual, use o seguinte comando, que adiciona o sinalizador `--vnet-peer`:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Caso você receba um erro que o nome do host não está disponível, talvez seja porque o nome do cluster não é exclusivo. Tente excluir o registro do aplicativo original e refazer as etapas com um nome de cluster diferente em [Criar um novo registro do aplicativo] (howto-aad-app-configuration.md#create-a-new-app-registration), omitindo a etapa final de criação de um novo usuário e grupo de segurança.

Depois de alguns minutos, `az openshift create` será concluído.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Obter a URL de entrada para seu cluster

Executando o comando a seguir, obtenha a URL para entrar em seu cluster:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Procure o `publicHostName` na saída, por exemplo: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

A URL de entrada para seu cluster será `https://`, seguida do valor `publicHostName`.  Por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Você usará esse URI na próxima etapa como parte do URI de redirecionamento de registro de aplicativo.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Etapa 3: Atualizar o URI de redirecionamento de registro de aplicativo

Agora que você tem a URL de entrada para o cluster, defina a interface do usuário de redirecionamento de registro de aplicativo:

1. Abra a [folha Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Clique no objeto de registro de aplicativo.
3. Clique em **Adicionar um URI de redirecionamento**.
4. Certifique-se de que o **TIPO** é **Web** e defina o **URI DE REDIRECIONAMENTO** usando o seguinte padrão: `https://<public host name>/oauth2callback/Azure%20AD`. Por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Clique em **Salvar**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Etapa 4: Entrar no console do OpenShift

Agora você está pronto para entrar no console do OpenShift com o novo cluster. O [Console Web OpenShift ](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) possibilita visualizar, procurar e gerenciar o conteúdo dos projetos do OpenShift.

Será necessária uma nova instância do navegador que ainda não armazenou em cache a identidade que você normalmente usa para entrar no portal do Azure.

1. Abra uma janela *anônimo* (Chrome) ou janela *InPrivate* (Microsoft Edge).
2. Navegue até a URL de logon que você obteve acima, por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Entre usando o nome de usuário que você criou na etapa 3 de [Criar um novo usuário do Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Uma caixa de diálogo **Permissões solicitadas** será exibida. Clique em **Consentir em nome de sua organização** e, em seguida, em **Aceitar**.

Agora você está conectado ao console do cluster.

![Captura de tela do console do cluster do OpenShift](./media/aro-console.png)

 Saiba mais sobre [como usar o console do OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) para criar e compilar imagens na documentação do [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html).

## <a name="step-5-install-the-openshift-cli"></a>Etapa 5: Instalar a CLI do OpenShift

A [CLI do OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (ou *Ferramentas de OC*) fornecem comandos para gerenciar os aplicativos e utilitários de nível inferior de modo a interagir com os diversos componentes do cluster do OpenShift.

No console do OpenShift, clique no ponto de interrogação no canto superior direito perto do seu nome de entrada e selecione **Ferramentas da Linha de Comando**.  Siga o link **Versão Mais Recente** para baixar e instalar a CLI de oc com suporte para Linux, MacOS ou Windows.

> [!NOTE]
> Se você não visualizar o ícone de ponto de interrogação no canto superior direito, selecione *Catálogo de Serviços* ou *Console do Aplicativo* na lista suspensa no canto superior esquerdo.
>
> Como alternativa, é possível [baixar a CLI de oc](https://www.okd.io/download.html) diretamente.

A página **Ferramentas da Linha de Comando** fornece um comando do formato `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Clique no botão *Copiar para área de transferência* para copiar esse comando.  Em uma janela de terminal, [defina o caminho](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) para incluir a instalação local das ferramentas de oc. Em seguida, entre no cluster usando o comando CLI de oc copiado.

Se não foi possível obter o valor do token usando as etapas acima, obtenha o valor do token de: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um cluster do Red Hat OpenShift no Azure

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Dimensionar um cluster do Red Hat OpenShift no Azure](tutorial-scale-cluster.md)

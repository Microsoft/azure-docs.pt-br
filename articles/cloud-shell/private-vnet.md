---
title: Cloud Shell em uma rede virtual do Azure
description: Implantar Cloud Shell em uma rede virtual do Azure
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 58f6c7a3b5d68d2825cead545ba1b683d1faf1af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222795"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Implantar Cloud Shell em uma rede virtual do Azure

Uma sessão de Cloud Shell regular é executada em um contêiner em uma rede da Microsoft separada dos seus recursos. Isso significa que os comandos em execução dentro do contêiner não podem acessar recursos que só podem ser acessados de uma rede virtual específica. Por exemplo, você não pode usar o SSH para se conectar de Cloud Shell a uma máquina virtual que tem apenas um endereço IP privado ou usar kubectl para se conectar a um cluster kubernetes que bloqueou o acesso. 

Esse recurso opcional resolve essas limitações e permite que você implante Cloud Shell em uma rede virtual do Azure que você controla. A partir daí, o contêiner é capaz de interagir com recursos na rede virtual que você selecionar.  

Abaixo, você pode ver a arquitetura de recursos que será implantada e usada neste cenário.

![Ilustra o Cloud Shell arquitetura de VNET isolada.](media/private-vnet/data-diagram.png)

Antes de poder usar Cloud Shell em sua própria rede virtual do Azure, você precisará criar vários recursos para dar suporte a essa funcionalidade. Este artigo mostra como configurar os recursos necessários usando um modelo ARM.

> [!NOTE]
> Esses recursos só precisam ser configurados uma vez para a rede virtual. Em seguida, eles podem ser compartilhados por todos os administradores com acesso à rede virtual.

## <a name="required-network-resources"></a>Recursos de rede necessários

### <a name="virtual-network"></a>Rede virtual
Uma rede virtual define o espaço de endereço no qual uma ou mais sub-redes são criadas.

A rede virtual desejada a ser usada para Cloud Shell precisa ser identificada. Normalmente, essa será uma rede virtual existente que contém os recursos que você gostaria de gerenciar ou uma rede que possua redes que contêm seus recursos.

### <a name="subnet"></a>Sub-rede
Dentro da rede virtual selecionada, uma sub-rede dedicada deve ser usada para contêineres de Cloud Shell. Essa sub-rede é delegada para o serviço ACI (instâncias de contêiner do Azure).  Quando um usuário solicita um contêiner de Cloud Shell em uma rede virtual, Cloud Shell usa ACI para criar um contêiner que está nessa sub-rede delegada.  Nenhum outro recurso pode ser criado nessa sub-rede.

### <a name="network-profile"></a>Perfil de rede
Um perfil de rede é um modelo de configuração de rede para recursos do Azure que especifica determinadas propriedades de rede para o recurso.

### <a name="azure-relay"></a>Retransmissão do Azure
Uma [retransmissão do Azure](../azure-relay/relay-what-is-it.md) permite que dois pontos de extremidade que não podem ser acessados diretamente se comuniquem. Nesse caso, ele é usado para permitir que o navegador do administrador se comunique com o contêiner na rede privada.

A instância de retransmissão do Azure usada para Cloud Shell pode ser configurada para controlar quais redes podem acessar recursos de contêiner: 
- Acessível pela Internet pública: nessa configuração, Cloud Shell fornece uma maneira de acessar recursos internos de outra forma de fora. 
- Acessível a partir de redes especificadas: nessa configuração, os administradores terão que acessar o portal do Azure de um computador em execução na rede apropriada para poder usar Cloud Shell.

## <a name="storage-requirements"></a>Requisitos de armazenamento
Como no Cloud Shell padrão, uma conta de armazenamento é necessária ao usar Cloud Shell em uma rede virtual. Cada administrador precisa de um compartilhamento de arquivos para armazenar seus arquivos.  A conta de armazenamento precisa ser acessível a partir da rede virtual usada pelo Cloud Shell. 

## <a name="virtual-network-deployment-limitations"></a>Limitações da implantação na rede virtual
* Devido aos recursos de rede adicionais envolvidos, a inicialização Cloud Shell em uma rede virtual normalmente é mais lenta do que uma sessão de Cloud Shell padrão.

* No momento, há suporte para todas as regiões de Cloud Shell além da Índia central. 

* A [retransmissão do Azure](../azure-relay/relay-what-is-it.md) não é um serviço gratuito, veja seus [preços](https://azure.microsoft.com/pricing/details/service-bus/). No cenário de Cloud Shell, uma conexão híbrida é usada para cada administrador enquanto estiver usando Cloud Shell. A conexão será desligada automaticamente após a conclusão da sessão de Cloud Shell.

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

O provedor de recursos Microsoft. ContainerInstances precisa ser registrado na assinatura que contém a rede virtual que você deseja usar. Selecione a assinatura apropriada com `Set-AzContext -Subscription {subscriptionName}` e, em seguida, execute:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Se **RegistrationState** for `Registered` , nenhuma ação será necessária. Se for `NotRegistered` , execute `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` . 

## <a name="deploy-network-resources"></a>Implantar recursos de rede
 
### <a name="create-a-resource-group-and-virtual-network"></a>Criar um grupo de recursos e uma rede virtual
Se você já tiver uma VNET desejada à qual deseja se conectar, pule esta seção.

No portal do Azure ou usando CLI do Azure, Azure PowerShell, etc. criar um grupo de recursos e uma rede virtual no novo grupo de recursos, **o grupo de recursos e a rede virtual precisam estar na mesma região**.

### <a name="arm-templates"></a>Modelos de ARM
Utilize o [modelo de início rápido do Azure](https://aka.ms/cloudshell/docs/vnet/template) para criar Cloud Shell recursos em uma rede virtual e o [modelo de início rápido do Azure](https://aka.ms/cloudshell/docs/vnet/template/storage) para criar o armazenamento necessário. Anote os nomes dos recursos, principalmente o nome do compartilhamento de arquivos.

### <a name="open-relay-firewall"></a>Abrir firewall de retransmissão
Navegue até a retransmissão criada usando o modelo acima, selecione "rede" em configurações, permitir o acesso da rede do navegador à retransmissão. Por padrão, a retransmissão só é acessível da rede virtual na qual foi criada. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Configurar Cloud Shell para usar uma rede virtual.
> [!NOTE]
> Esta etapa deve ser concluída para cada administrador, que usará Cloud Shell.

Após a implantação concluindo as etapas acima, navegue até Cloud Shell no portal do Azure ou em https://shell.azure.com . Uma dessas experiências deve ser usada cada vez que você quiser se conectar a uma experiência de Cloud Shell isolada.

> [!NOTE]
> Se Cloud Shell tiver sido usado no passado, o CloudDrive existente deverá ser desmontado. Para fazer isso `clouddrive unmount` , execute em uma sessão de Cloud Shell ativa, atualize sua página.

Conecte-se ao Cloud Shell, você será avisado com a primeira experiência de execução. Selecione sua experiência de shell preferida, selecione "Mostrar configurações avançadas" e selecione a caixa "Mostrar configurações de isolamento de VNET". Preencha os campos no pop-up.  A maioria dos campos fará preenchimento automático dos recursos disponíveis que podem ser associados a Cloud Shell em uma rede virtual.  O nome do compartilhamento de arquivos precisará ser preenchido pelo usuário.


![Ilustra as Cloud Shell configurações de primeira experiência da VNET isolada.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre as redes virtuais do Azure](../virtual-network/virtual-networks-overview.md)

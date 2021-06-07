---
title: Instalar o gateway de dados local para Azure Analysis Services | Microsoft Docs
description: Saiba como instalar e configurar um gateway de dados local para se conectar a fontes de dados locais de um servidor Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 64bd9e4a4cf78d2628e946af30c2d290ff002cf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081137"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados local

Um gateway de dados local é necessário quando um ou mais servidores do Azure Analysis Services na mesma região se conectam a fontes de dados locais.  Embora o gateway que você instalar seja o mesmo usado por outros serviços como Power BI, Power apps e aplicativos lógicos, ao instalar o para Azure Analysis Services, há algumas etapas adicionais que você precisa concluir. Este artigo de instalação é específico para **Azure Analysis Services**. 

Para saber mais sobre como Azure Analysis Services funciona com o gateway, consulte [conectando-se a fontes de dados locais](analysis-services-gateway.md). Para saber mais sobre os cenários de instalação avançada e o gateway em geral, consulte [documentação de gateways de dados locais](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Pré-requisitos

**Requisitos mínimos:**

* .NET 4.5 Framework
* versão de 64 bits do Windows 8/Windows Server 2012 R2 (ou posterior)

**Recomendado:**

* CPU de 8 núcleos
* Memória de 8 GB
* versão de 64 bits do Windows 8/Windows Server 2012 R2 (ou posterior)

**Considerações importantes:**

* Durante a instalação, ao registrar o gateway no Azure, a região padrão de sua assinatura será selecionada. Você pode escolher uma assinatura e uma região diferentes. Se tiver servidores em mais de uma região, você precisará instalar um gateway para cada região. 
* O gateway não pode ser instalado em um controlador de domínio.
* Apenas um gateway pode ser instalado em um computador.
* Instale o gateway em um computador que permanece ligado e não entra em suspensão.
* Não instale o gateway em um computador com uma conexão somente sem fio com a rede. O desempenho pode ser prejudicado.
* Ao instalar o gateway, a conta de usuário com a qual você está conectado ao computador precisa ter privilégios de Fazer logon como um serviço. Quando a instalação é concluída, o serviço de gateway de dados local usa a conta NT SERVICE\PBIEgwService para fazer logon como um serviço. Uma conta diferente pode ser especificada durante a instalação ou nos Serviços após a instalação ser concluída. Assegure-se que as configurações de Política de Grupo permitam que tanto a conta com a qual você está conectado ao instalar quanto a conta de serviço escolhida tenham privilégios de Fazer logon como um serviço.
* Entre no Azure com uma conta do Azure AD que seja do mesmo [locatário](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) que a assinatura que você está usando para registrar o gateway. Contas B2B (de convidado) do Azure não têm suporte durante a instalação e registro do gateway.
* Se a fontes de dados estiverem em uma Rede Virtual (VNet) do Azure, você deverá configurar a propriedade de servidor [AlwaysUseGateway](analysis-services-vnet-gateway.md).

## <a name="download"></a>Baixar

 [Baixe o gateway](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Instalar

1. Execute a instalação.

2. Selecione **Gateway de dados local**.

   ![Selecionar](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecione um local, aceite os termos e, em seguida, clique em **Instalar**.

   ![Instale os termos de licença e de local](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Entre no Azure. A conta deve estar no Azure Active Directory de seu locatário. Essa conta é usada para o administrador do gateway. Contas B2B (de convidado) do Azure não têm suporte durante a instalação e registro do gateway.

   ![Entrar no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se você entrar com uma conta de domínio, ela será mapeada para sua conta organizacional no Azure AD. Sua conta organizacional é usada como administrador do gateway.

## <a name="register"></a>Registrar

Para criar um recurso de gateway no Azure, você precisa registrar a instância local que instalou com o Serviço de Nuvem do Gateway. 

1.  Selecione **Registrar um novo gateway neste computador**.

    ![Captura de tela que realça a opção registrar um novo gateway neste computador.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digite um nome e uma chave de recuperação para o gateway. Por padrão, o gateway usa a região padrão de sua assinatura. Se precisar selecionar uma região diferente, selecione **Alterar Região**.

    > [!IMPORTANT]
    > Salve sua chave de recuperação em um local seguro. A chave de recuperação é necessária para controlar, migrar ou restaurar um gateway. 

   ![Registrar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Criar um recurso de gateway do Azure

Depois de instalar e registrar seu gateway, você precisará criar um recurso de gateway no Azure. Entre no Azure com a mesma conta usada ao registrar o gateway.

1. Em portal do Azure, clique em **criar um recurso**, procure por **Gateway de dados local** e, em seguida, clique em **criar**.

   ![Criar um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Em **Criar gateway de conexão**, insira estas configurações:

   * **Nome**: insira um nome para o recurso do gateway. 

   * **Assinatura**: selecione a assinatura do Azure a associar ao seu recurso de gateway. 
   
     A assinatura padrão baseia-se na conta do Azure utilizada para entrar.

   * **Grupo de recursos**: Crie um novo grupo de recursos ou escolha um grupo de recursos existente.

   * **Local**: selecione a região em que você registrou o gateway.

   * **Nome da instalação**: se a instalação do gateway ainda não estiver selecionada, selecione o gateway que você instalou no computador e registrado. 

     Quando tiver concluído, clique em **Criar**.

## <a name="connect-gateway-resource-to-server"></a>Conectar o recurso de gateway ao servidor

> [!NOTE]
> Não há suporte para a conexão com um recurso de gateway em uma assinatura diferente do servidor no portal, mas tem suporte usando o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na visão geral de seu servidor do Azure Analysis Services, clique em **Gateway de Dados Local**.

   ![Conectar servidor ao gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Em **Escolha um Gateway de Dados Local para conectar**, selecione o recurso de gateway e, em seguida, clique em **Conectar gateway selecionado**.

   ![Conectar servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o gateway não aparecer na lista, provavelmente seu servidor não está na mesma região que você especificou ao registrar o gateway.

    Quando a conexão entre o seu servidor e o recurso de gateway for bem-sucedida, o status mostrará **conectado**.


    ![Êxito ao conectar o servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use [Get-AzResource](/powershell/module/az.resources/get-azresource) para obter a ResourceId do gateway. Em seguida, conecte o recurso de gateway a um servidor novo ou existente especificando **-GatewayResourceID** em [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) ou [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver).

Para obter a ID de recurso do gateway:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

Para configurar um servidor existente:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

É isso. Se precisar abrir portas ou solucionar qualquer problema, não deixe de conferir [Gateway de dados local](analysis-services-gateway.md).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar o Analysis Services](analysis-services-manage.md)   
* [Obter dados do Azure Analysis Services](analysis-services-connect.md)   
* [Usar gateway para fontes de dados em uma Rede Virtual do Azure](analysis-services-vnet-gateway.md)
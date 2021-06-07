---
title: Habilitar o acesso a sites particulares no Azure Functions
description: Saiba como configurar o acesso a sites particulares da rede virtual do Azure para o Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 766ad12daeb6d2763f7ed5fe026cd4a0021eaf33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937035"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Tutorial: Estabelecer o acesso a sites particulares do Azure Functions

Este tutorial mostra como habilitar o [acesso a sites particulares](./functions-networking-options.md#private-endpoint-connections) com o Azure Functions. Usando o acesso a sites particulares, você pode exigir que o código de função seja disparado apenas de uma rede virtual específica.

O acesso a sites particulares é útil em cenários quando o acesso ao aplicativo de funções precisa ser limitado a uma rede virtual específica. Por exemplo, o aplicativo de funções pode ser aplicável somente aos funcionários de uma organização específica ou aos serviços que estão dentro da rede virtual especificada (como outra Função do Azure, Máquina Virtual do Azure ou um cluster do AKS).

Se um aplicativo de funções precisar acessar recursos do Azure na rede virtual ou ser conectado por meio de [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md), a [integração de rede virtual](./functions-create-vnet.md) será necessária.

Neste tutorial, você aprenderá a configurar o acesso a sites particulares para o seu aplicativo de funções:

> [!div class="checklist"]
> * Criar uma máquina virtual
> * Criar um serviço do Azure Bastion
> * Criar um aplicativo do Azure Functions
> * Configurar um ponto de extremidade de serviço de rede virtual
> * Criar e implantar uma Função do Azure
> * Invocar a função dentro e fora da rede virtual

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="topology"></a>Topologia

O seguinte diagrama mostra a arquitetura da solução a ser criada:

![Diagrama da arquitetura de alto nível da solução de acesso a sites particulares](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que você tenha noções básicas do endereçamento IP e das sub-redes. Você pode começar com [este artigo que aborda os conceitos básicos de endereçamento e sub-redes](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos outros artigos e vídeos estão disponíveis online.

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

A primeira etapa deste tutorial é criar uma máquina virtual dentro de uma rede virtual.  A máquina virtual será usada para acessar a função depois que você restringir o acesso para que ele fique disponível apenas dentro da rede virtual.

1. Selecione o botão **Criar um recurso**.

1. No campo de pesquisa, digite **Windows Server** e selecione **Windows Server** nos resultados da pesquisa.

1. Selecione **Windows Server 2019 Datacenter** na lista de opções do Windows Server e selecione o botão **Criar**.

1. Na guia _Informações Básicas_, use as configurações da VM, conforme especificado na tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![Guia Informações Básicas de uma nova VM do Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Assinatura_ | Sua assinatura | A assinatura na qual os recursos são criados. |
    | [_Grupo de recursos_](../azure-resource-manager/management/overview.md) | myResourceGroup | Escolha o grupo de recursos que conterá os recursos deste tutorial.  O uso do mesmo grupo de recursos facilitará a limpeza de recursos quando você concluir este tutorial. |
    | _Nome da máquina virtual_ | myVM | O nome da VM precisa ser exclusivo no grupo de recursos |
    | [_Região_](https://azure.microsoft.com/regions/) | (EUA) Centro-Norte dos EUA | Escolha uma região perto de você ou perto das funções a serem acessadas. |
    | _Portas de entrada públicas_ | Nenhum | Selecione **Nenhum** para garantir que não haja conectividade de entrada com a VM pela Internet. O acesso remoto à VM será configurado por meio do serviço do Azure Bastion. |

1. Escolha a guia _Rede_ e selecione **Criar** para configurar uma nova rede virtual.

    >[!div class="mx-imgBorder"]
    >![Captura de tela que mostra a guia "Rede" com a ação "Criar" realçada na seção "Rede virtual".](./media/functions-create-private-site-access/create-vm-networking.png)

1. Em _Criar rede virtual_, use as configurações na tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![Criar uma rede virtual para a nova VM](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Nome_ | myResourceGroup-vnet | Use o nome padrão gerado para a rede virtual. |
    | _Intervalo de endereços_ | 10.10.0.0/16 | Use um só intervalo de endereços para a rede virtual. |
    | _Nome da sub-rede_ | Tutorial | Nome da sub-rede. |
    | _Intervalo de endereços_ (sub-rede) | 10.10.1.0/24 | O tamanho da sub-rede define o número de interfaces que podem ser adicionadas à sub-rede. Essa sub-rede é usada pela VM. Uma sub-rede /24 fornece 254 endereços de host. |

1. Selecione **OK** para criar a rede virtual.
1. De volta à guia _Rede_, verifique se **Nenhum** está selecionado em _IP Público_.
1. Escolha a guia _Gerenciamento_ e, em _Conta de armazenamento de diagnóstico_, escolha **Criar** para criar uma conta de armazenamento.
1. Mantenha os valores padrão nas seções _Identidade_, _Desligamento automático_ e _Backup_.
1. Selecione _Examinar + criar_. Depois de concluir a validação, selecione **Criar**. O processo de criação da VM leva alguns minutos.

## <a name="configure-azure-bastion"></a>Configurar o Azure Bastion

O [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) é um serviço do Azure totalmente gerenciado que fornece acesso seguro RDP e SSH a máquinas virtuais diretamente no portal do Azure. O uso do serviço do Azure Bastion elimina a necessidade de definir as configurações de rede relacionadas ao acesso RDP.

1. No portal, escolha **Adicionar** na parte superior da exibição do grupo de recursos.
1. No campo de pesquisa, digite **Bastion**.
1. Selecione **Bastion** nos resultados da pesquisa.
1. Selecione **Criar** para iniciar o processo de criação de um recurso do Azure Bastion. Você observará uma mensagem de erro na seção _Rede virtual_, pois ainda não há uma sub-rede AzureBastionSubnet. A sub-rede será criada nas etapas a seguir. Use as configurações da tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![Início da criação do Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Nome_ | myBastion | O nome do novo recurso do Bastion |
    | _Região_ | Centro-Norte dos EUA | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |
    | _Rede virtual_ | myResourceGroup-vnet | A rede virtual na qual o recurso do Bastion será criado |
    | _Sub-rede_ | AzureBastionSubnet | A sub-rede da rede virtual na qual o novo recurso de host do Bastion será implantado. É necessário criar uma sub-rede usando o valor de nome **AzureBastionSubnet**. Esse valor permite que o Azure saiba em qual sub-rede os recursos do Bastion serão implantados. É necessário usar uma sub-rede de pelo menos **/27** ou maior (/27,/26 e assim por diante). |

    > [!NOTE]
    > Para obter um guia passo a passo detalhado sobre como criar um recurso do Azure Bastion, consulte o tutorial [Criar um host do Azure Bastion](../bastion/tutorial-create-host-portal.md).

1. Crie uma sub-rede na qual o Azure possa provisionar o host do Azure Bastion. A escolha de **Gerenciar a configuração de sub-rede** abre um novo painel em que é possível definir uma nova sub-rede.  Escolha **+ Sub-rede** para criar uma sub-rede.
1. A sub-rede precisa ter o nome **AzureBastionSubnet** e o prefixo de sub-rede precisa ser, pelo menos, **/27**.  Selecione **OK** para criar a sub-rede.

    >[!div class="mx-imgBorder"]
    >![Criar a sub-rede para o host do Azure Bastion](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. Na página _Criar um Bastion_, selecione o **AzureBastionSubnet** recém-criado na lista de sub-redes disponíveis.

    >[!div class="mx-imgBorder"]
    >![Criar um host do Azure Bastion com uma sub-rede específica](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. Selecione **Examinar e Criar**. Após a conclusão da validação, selecione **Criar**. Serão necessários alguns minutos para que o recurso do Azure Bastion seja criado.

## <a name="create-an-azure-functions-app"></a>Criar um aplicativo do Azure Functions

A próxima etapa é criar um aplicativo de funções no Azure usando o [Plano de consumo](consumption-plan.md). Você implantará o código de função nesse recurso mais adiante no tutorial.

1. No portal, escolha **Adicionar** na parte superior da exibição do grupo de recursos.
1. Selecione **Computação > Aplicativo de Funções**
1. Na seção _Informações Básicas_, use as configurações do aplicativo de funções especificadas na tabela abaixo.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Grupo de recursos_ | myResourceGroup | Escolha o grupo de recursos que conterá os recursos deste tutorial.  O uso do mesmo grupo de recursos para o aplicativo de funções e a VM facilitará a limpeza de recursos quando você concluir este tutorial. |
    | _Nome do aplicativo de funções_ | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são a-z (não diferencia maiúsculas de minúsculas), 0-9 e -. |
    | _Publicar_ | Código | Opção para publicar arquivos de código ou um contêiner do Docker. |
    | _Pilha de runtime_ | Linguagem preferencial | Escolha um runtime compatível com sua linguagem de programação de funções favorita. |
    | _Região_ | Centro-Norte dos EUA | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

    Selecione **Próximo: Hospedagem >** .
1. Na seção _Hospedagem_, selecione a _Conta de armazenamento_, o _Sistema operacional_ e o _Plano_ adequados, conforme descrito na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Conta de armazenamento_ | Nome globalmente exclusivo | Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Use também uma conta existente, que precisará atender aos [requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements). |
    | _Sistema operacional_ | Sistema operacional preferencial | Um sistema operacional é pré-selecionado para você com base na seleção da pilha de runtime, mas você pode alterar a configuração, se necessário. |
    | _Plano_ | Consumo | O [plano de hospedagem](./functions-scale.md) determina como o aplicativo de funções é escalado e os recursos disponíveis para cada instância. |
1. Selecione **Examinar + Criar** para examinar as seleções de configuração de aplicativo.
1. Selecione **Criar** para provisionar e implantar o aplicativo de funções.

## <a name="configure-access-restrictions"></a>Configurar restrições de acesso

A próxima etapa é configurar [restrições de acesso](../app-service/app-service-ip-restrictions.md) para garantir que apenas os recursos na rede virtual possam invocar a função.

O acesso a [sites particulares](functions-networking-options.md#private-endpoint-connections) é habilitado pela criação de um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) da Rede Virtual do Azure entre o aplicativo de funções e a rede virtual especificada. As restrições de acesso são implementadas por meio de pontos de extremidade de serviço. Os pontos de extremidade de serviço garantem que apenas o tráfego proveniente da rede virtual especificada possa acessar o recurso designado. Nesse caso, o recurso designado é a Função do Azure.

1. No aplicativo de funções, selecione o link **Rede** no cabeçalho da seção _Configurações_.
1. A página _Rede_ é o ponto de partida para configurar o Azure Front Door, a CDN do Azure e também as Restrições de Acesso.
1. Selecione **Configurar Restrições de Acesso** para configurar o acesso a sites particulares.
1. Na página _Restrições de Acesso_, apenas a restrição padrão em vigor é exibida. O padrão não coloca nenhuma restrição no acesso ao aplicativo de funções.  Selecione **Adicionar regra** para criar uma configuração de restrição de acesso a sites particulares.
1. No painel _Adicionar Restrição de Acesso_, forneça um _Nome_, uma _Prioridade_ e _Descrição_ para a nova regra.
1. Selecione **Rede Virtual** na caixa suspensa _Tipo_ e a rede virtual criada anteriormente. Depois, selecione a sub-rede **Tutorial**. 
    > [!NOTE]
    > Podem ser necessários vários minutos para que o ponto de extremidade de serviço seja habilitado.
1. A página _Restrições de Acesso_ agora mostra que há uma nova restrição. Podem ser necessários alguns segundos para que o _Status do ponto de extremidade_ seja alterado de Desabilitado por meio do Provisionamento para Habilitado.

    >[!IMPORTANT]
    > Cada aplicativo de funções tem um [site de Ferramenta Avançada (Kudu)](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site) que é usado para gerenciar implantações de aplicativo de funções. Esse site é acessado em uma URL como: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Habilitar restrições de acesso no site Kudu impede a implantação do código do projeto de uma estação de trabalho de desenvolvedor local e, consequentemente, um agente é necessário na rede virtual para executar a implantação.

## <a name="access-the-functions-app"></a>Acessar o aplicativo de funções

1. Volte ao aplicativo de funções criado anteriormente.  Na seção _Visão Geral_, copie a URL.

    >[!div class="mx-imgBorder"]
    >![Obter a URL do aplicativo de funções](./media/functions-create-private-site-access/access-function-overview.png)

    Se você tentar acessar o aplicativo de funções agora no computador fora da rede virtual, receberá uma página HTTP 403 indicando que o acesso está proibido.
1. Volte para o grupo de recursos e selecione a máquina virtual criada anteriormente. Para acessar o site por meio da VM, é necessário se conectar à VM por meio do serviço do Azure Bastion.
1. Selecione **Conectar** e escolha **Bastion**.
1. Forneça o nome de usuário e a senha necessários para fazer logon na máquina virtual.
1. Selecione **Conectar**. Uma nova janela do navegador será exibida para permitir que você interaja com a máquina virtual.
É possível acessar o site no navegador da Web na VM porque a VM está acessando o site por meio da rede virtual.  Embora o site só possa ser acessado na rede virtual designada, uma entrada DNS pública permanece.

## <a name="create-a-function"></a>Criar uma função

A próxima etapa deste tutorial será criar uma Função do Azure disparada por HTTP. A invocação da função por meio de um HTTP GET ou POST deverá resultar em uma resposta "Olá, {nome}".  

1. Siga um dos inícios rápidos a seguir para criar e implantar seu aplicativo do Azure Functions.

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Linha de comando](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. Ao publicar seu projeto do Azure Functions, escolha o recurso de aplicativo de funções criado anteriormente neste tutorial.
1. Verifique se a função foi implantada.

    >[!div class="mx-imgBorder"]
    >![Função implantada na lista de funções](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Invocar a função diretamente

1. Para testar o acesso à função, você precisa copiar a URL da função. Selecione a função implantada e escolha **Obter URL da função**. Em seguida, clique no botão **Copiar** para copiar a URL para a área de transferência.

    >[!div class="mx-imgBorder"]
    >![Copiar a URL da função](./media/functions-create-private-site-access/get-function-url.png)

1. Cole a URL em um navegador da Web. Quando você tentar acessar o aplicativo de funções agora em um computador fora da rede virtual, receberá uma resposta HTTP 403 indicando que o acesso ao aplicativo está proibido.

## <a name="invoke-the-function-from-the-virtual-network"></a>Invocar a função na rede virtual

O acesso à função por meio de um navegador da Web (usando o serviço do Azure Bastion) na VM configurada na rede virtual resulta em êxito.

>[!div class="mx-imgBorder"]
>![Acessar a Função do Azure por meio do Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas


> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de rede do Functions](./functions-networking-options.md)
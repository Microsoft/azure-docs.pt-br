---
title: Integrar o Azure Functions com uma rede virtual do Azure
description: Um tutorial passo a passo que mostra como se conectar a uma função a uma rede virtual do Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0a31b58a3c843a2add0c84dc1a3ad4ab6417815e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612880"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Tutorial: integrar funções com uma rede virtual do Azure

Este tutorial mostra como usar o Azure Functions para conectar-se aos recursos em uma rede virtual do Azure. Você criará uma função que tenha acesso à internet e a uma VM executando o WordPress na rede virtual.

> [!div class="checklist"]
> * Criar um aplicativo de funções no plano Premium
> * Implantar um site WordPress a VM em uma rede virtual
> * Conectar o aplicativo de função para a rede virtual
> * Criar um proxy de função para acessar os recursos do WordPress
> * Solicitar um arquivo do WordPress de dentro da rede virtual

> [!NOTE]  
> Este tutorial cria um aplicativo de funções no plano Premium. Este plano de hospedagem está atualmente em visualização. Para obter mais informações, consulte [plano Premium].

## <a name="topology"></a>Topologia

O diagrama a seguir mostra a arquitetura da solução que você cria:

 ![Interface do usuário para integração da rede virtual](./media/functions-create-vnet/topology.png)

Funções em execução no plano Premium têm os mesmos recursos de hospedagem como aplicativos web no serviço de aplicativo do Azure, que inclui o recurso de integração de rede virtual. Para saber mais sobre a integração de rede virtual, incluindo a solução de problemas e configuração avançada, consulte [integrar seu aplicativo com uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que você compreenda o endereçamento IP e sub-redes. Você pode iniciar com [deste artigo que aborda os conceitos básicos de endereçamento e subrede](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos que mais artigos e vídeos estão disponíveis online.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar um aplicativo de funções em um plano Premium

Primeiro, crie um aplicativo de funções na [plano Premium]. Esse plano fornece escala sem servidor e dar suporte a integração da rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Você pode fixar o aplicativo de funções ao painel selecionando o ícone de pino no canto superior direito. Fixando torna mais fácil retornar a este aplicativo de função depois de criar sua VM.

## <a name="create-a-vm-inside-a-virtual-network"></a>Criar uma máquina virtual dentro de uma rede virtual

Em seguida, crie uma VM pré-configurada que executa o WordPress em uma rede virtual ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) por Jetware). Uma VM do WordPress for usada por causa de sua baixo custo e a conveniência. Esse mesmo cenário funciona com qualquer recurso em uma rede virtual, como APIs REST, ambientes de serviço de aplicativo e outros serviços do Azure. 

1. No portal, escolha **+ criar um recurso** no painel de navegação à esquerda, no campo de pesquisa, digite `WordPress LEMP7 Max Performance`, e pressione Enter.

1. Escolher **Wordpress LEMP Max Performance** nos resultados da pesquisa. Selecione um plano de software da **Wordpress LEMP Max Performance para CentOS** como o **plano de Software** e selecione **criar**.

1. No **Noções básicas sobre** guia, use as configurações da VM conforme especificado na tabela abaixo da imagem:

    ![Guia de Noções básicas para criar uma VM](./media/functions-create-vnet/create-vm-1.png)

    | Configuração      | Valor sugerido  | DESCRIÇÃO      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura em que seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Escolha `myResourceGroup`, ou o grupo de recursos que você criou com o seu aplicativo de funções. Usando o mesmo grupo de recursos para o aplicativo de função, a VM do WordPress e o plano de hospedagem torna mais fácil limpar os recursos quando você concluir este tutorial. |
    | **Nome da máquina virtual** | VNET-Wordpress | O nome da VM precisa ser exclusivo no grupo de recursos |
    | **[Região](https://azure.microsoft.com/regions/)** | Oeste da Europa (Europa) | Escolha uma região perto de você ou perto as funções que acessar a VM. |
    | **Size** | B1s | Escolher **alterar o tamanho** e, em seguida, selecione a imagem B1s standard, que tem 1 vCPU e 1 GB de memória. |
    | **Tipo de autenticação** | Senha | Para usar a autenticação de senha, você também deve especificar um **nome de usuário**, um seguro **senha**e então **Confirmar senha**. Para este tutorial, você não precisará entrar para a VM, a menos que você precise solucionar. |

1. Escolha o **Networking** guia e em redes virtuais de configurar, selecione **criar novo**.

1. Na **criar rede virtual**, use as configurações na tabela abaixo da imagem:

    ![Guia de rede de criar a VM](./media/functions-create-vnet/create-vm-2.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | Você pode usar o nome padrão gerado para sua rede virtual. |
    | **Intervalo de endereços** | 10.10.0.0/16 | Use um intervalo de endereço único para a rede virtual. |
    | **Nome da sub-rede** | Tutorial-Net | Nome da sub-rede. |
    | **Intervalo de endereços** (sub-rede) | 10.10.1.0/24   | O tamanho da sub-rede define quantas interfaces podem ser adicionados à sub-rede. Essa sub-rede é usada pelo site de WordPress.  Um `/24` sub-rede fornece 254 endereços de host. |

1. Selecione **Okey** para criar a rede virtual.

1. Volta a **Networking** guia, escolha **None** para **IP público**.

1. Escolha o **Management** guia, em seguida, na **conta de armazenamento de diagnóstico**, escolha a conta de armazenamento que você criou com o seu aplicativo de funções.

1. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **criar**. A VM criar processo leva alguns minutos. A VM criada só pode acessar a rede virtual.

1. Depois que a VM é criada, escolha **ir para o recurso** para exibir a página para sua nova VM, em seguida, escolha **Networking** sob **configurações**.

1. Verifique se não há nenhuma **IP público**. Lembre-se a **IP privado**, que você usa para se conectar à VM de seu aplicativo de funções.

    ![Configurações de rede na VM](./media/functions-create-vnet/vm-networking.png)

Agora você tem um site do WordPress implantado inteiramente dentro de sua rede virtual. Este site não está acessível pela internet pública.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Conectar seu aplicativo de função para a rede virtual

Com um site do WordPress em execução em uma VM em uma rede virtual, agora você pode conectar seu aplicativo de funções nessa rede virtual.

1. Em seu novo aplicativo de função, selecione **recursos da plataforma** > **rede**.

    ![Escolha a rede no aplicativo de funções](./media/functions-create-vnet/networking-0.png)

1. Sob **integração de VNet**, selecione **clique aqui para configurar**.

    ![Status de configuração de um recurso de rede](./media/functions-create-vnet/Networking-1.png)

1. Na página de integração de rede virtual, selecione **Adicionar rede virtual (versão prévia)** .

    ![Adicionar a visualização de integração de rede virtual](./media/functions-create-vnet/networking-2.png)

1. Na **Status do recurso de rede**, use as configurações na tabela abaixo da imagem:

    ![Definir a rede virtual do aplicativo de função](./media/functions-create-vnet/networking-3.png)

    | Configuração      | Valor sugerido  | DESCRIÇÃO      |
    | ------------ | ---------------- | ---------------- |
    | **Rede Virtual** | MyResourceGroup-vnet | Essa rede virtual é aquele que você criou anteriormente. |
    | **Sub-rede** | Criar uma nova sub-rede | Crie uma sub-rede na rede virtual para seu aplicativo de funções usar. Integração de rede virtual deve ser configurada para usar uma sub-rede vazia. Não importa que suas funções usam uma sub-rede diferente de sua VM. A rede virtual roteia automaticamente o tráfego entre as duas sub-redes. |
    | **Nome da sub-rede** | Função-Net | Nome da nova sub-rede. |
    | **Bloco de endereço de rede virtual** | 10.10.0.0/16 | Escolha o mesmo bloco de endereço usado pelo site de WordPress. Você deve ter apenas um bloco de endereço definido. |
    | **Intervalo de endereços** | 10.10.2.0/24   | O tamanho da sub-rede restringe o número total de instâncias que seu aplicativo de funções de plano Premium pode escalar horizontalmente para. Este exemplo usa um `/24` sub-rede com 254 endereços disponíveis no host. Essa sub-rede é provisionados em excesso, mas é fácil calcular. |

1. Selecione **Okey** para adicionar a sub-rede. Feche as páginas de integração de rede virtual e o Status do recurso de rede para retornar à página de seu aplicativo de função.

O aplicativo de função agora pode acessar a rede virtual onde o site do WordPress está sendo executado. Em seguida, use [Proxies do Azure Functions](functions-proxies.md) para retornar um arquivo do site do WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Criar um proxy para acessar os recursos VM

Com a integração de rede virtual habilitado, você pode criar um proxy em seu aplicativo de função para encaminhar solicitações para a VM em execução na rede virtual.

1. Em seu aplicativo de função, selecione **Proxies** >  **+** , em seguida, use as configurações de proxy na tabela abaixo da imagem:

    ![Definir as configurações de proxy](./media/functions-create-vnet/create-proxy.png)

    | Configuração  | Valor sugerido  | Descrição      |
    | -------- | ---------------- | ---------------- |
    | **Nome** | Planta | O nome pode ser qualquer valor. Ele é usado para identificar o proxy. |
    | **Modelo de rota** | /plant | Rota que é mapeado para um recurso VM. |
    | **URL de back-end** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Substitua `<YOUR_VM_IP>` com o endereço IP da sua VM do WordPress que você criou anteriormente. Esse mapeamento retorna um único arquivo do site. |

1. Selecione **criar** para adicionar o proxy ao seu aplicativo de funções.

## <a name="try-it-out"></a>Experimentar

1. Em seu navegador, tente acessar a URL usada como o **URL de back-end**. Conforme o esperado, a solicitação expira. Um tempo limite ocorre porque o site do WordPress é conectado somente à sua rede virtual e não pela internet.

1. Cópia de **URL do Proxy** seu novo proxy de valor e cole-o na barra de endereços do navegador. A imagem retornada é do site de WordPress em execução dentro de sua rede virtual.

    ![Arquivo de imagem de fábrica retornado do site do WordPress](./media/functions-create-vnet/plant.png)

Seu aplicativo de funções está conectado à internet e sua rede virtual. O proxy está recebendo uma solicitação pela internet pública e, em seguida, que atua como um proxy HTTP simples para encaminhar a solicitação para rede virtual conectada. O proxy, em seguida, retransmite a resposta de volta para você publicamente na Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o site do WordPress serve como uma API que é chamada usando um proxy no aplicativo de funções. Esse cenário faz um bom tutorial porque é fácil de configurar e visualizar. Você pode usar qualquer outra API implantada em uma rede virtual. Você também poderia ter criado uma função com o código que chama as APIs implantadas dentro da rede virtual. Um cenário mais realista é uma função que usa APIs de cliente de dados para chamar uma instância do SQL Server implantada na rede virtual.

Funções em execução em um plano Premium compartilham a mesma infraestrutura subjacente do serviço de aplicativo como aplicativos web em planos de PremiumV2. Toda a documentação para [aplicativos web no serviço de aplicativo do Azure](../app-service/overview.md) aplica-se a suas funções de plano Premium.

> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de rede em funções](./functions-networking-options.md)

[Plano Premium]: functions-scale.md#premium-plan

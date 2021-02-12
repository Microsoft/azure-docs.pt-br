---
title: Integrar Azure Functions com uma rede virtual do Azure
description: Um tutorial passo a passo que mostra como conectar uma função a uma rede virtual do Azure
ms.topic: article
ms.date: 4/23/2020
ms.openlocfilehash: efc936111d162d73b1cc5465ae6b677c9006ab32
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937002"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Tutorial: Integrar o Functions a uma rede virtual do Azure

Este tutorial mostra como usar Azure Functions para se conectar a recursos em uma rede virtual do Azure. Você criará uma função que tem acesso à Internet e a uma VM que executa o WordPress na rede virtual.

> [!div class="checklist"]
> * Criar um aplicativo de funções no plano Premium
> * Implantar um site do WordPress na VM em uma rede virtual
> * Conectar o aplicativo de funções à rede virtual
> * Criar um proxy de função para acessar recursos do WordPress
> * Solicitar um arquivo do WordPress de dentro da rede virtual

## <a name="topology"></a>Topologia

O diagrama a seguir mostra a arquitetura da solução que você cria:

 ![Interface do usuário para integração de rede virtual](./media/functions-create-vnet/topology.png)

As funções em execução no plano Premium têm os mesmos recursos de hospedagem que os aplicativos Web no serviço Azure App, que inclui o recurso de integração VNet. Para saber mais sobre a integração VNet, incluindo solução de problemas e configuração avançada, confira [integrar seu aplicativo a uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que você tenha noções básicas do endereçamento IP e das sub-redes. Você pode começar com [este artigo que aborda os conceitos básicos de endereçamento e sub-redes](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos outros artigos e vídeos estão disponíveis online.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar um aplicativo de funções em um plano Premium

Primeiro, você cria um aplicativo de funções no [plano Premium]. Este plano fornece escala sem servidor enquanto dá suporte à integração de rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Você pode fixar o aplicativo de funções no painel selecionando o ícone de pino no canto superior direito. A fixação torna mais fácil retornar a esse aplicativo de funções depois de criar sua VM.

## <a name="create-a-vm-inside-a-virtual-network"></a>Criar uma VM dentro de uma rede virtual

Em seguida, crie uma VM pré-configurada que executa o WordPress dentro de uma rede virtual ([WordPress LEMP7 desempenho máximo](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) por Jetware). Uma VM do WordPress é usada devido a seu baixo custo e conveniência. Esse mesmo cenário funciona com qualquer recurso em uma rede virtual, como APIs REST, ambientes de serviço de aplicativo e outros serviços do Azure. 

1. No portal, escolha **+ criar um recurso** no painel de navegação à esquerda, no tipo de campo de pesquisa `WordPress LEMP7 Max Performance` e pressione Enter.

1. Escolha o **desempenho máximo do WordPress LEMP** nos resultados da pesquisa. Selecione um plano de software de **desempenho do WordPress LEMP Max para centos** como o **plano de software** e selecione **criar**.

1. Na guia **Informações Básicas**, use as configurações da VM, conforme especificado na tabela abaixo da imagem:

    ![Guia básico para criar uma VM](./media/functions-create-vnet/create-vm-1.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha `myResourceGroup` ou o grupo de recursos que você criou com seu aplicativo de funções. Usar o mesmo grupo de recursos para o aplicativo de funções, a VM do WordPress e o plano de hospedagem facilita a limpeza de recursos quando você terminar este tutorial. |
    | **Nome da máquina virtual** | VNET-Wordpress | O nome da VM precisa ser exclusivo no grupo de recursos |
    | **[Região](https://azure.microsoft.com/regions/)** | Européia Europa Ocidental | Escolha uma região perto de você ou perto das funções que acessam a VM. |
    | **Tamanho** | B1s | Escolha **alterar tamanho** e, em seguida, selecione a imagem B1s padrão, que tem 1 vCPU e 1 GB de memória. |
    | **Tipo de autenticação** | Senha | Para usar a autenticação de senha, você também deve especificar um **nome de usuário**, uma **senha** segura e, em seguida, **confirmar a senha**. Para este tutorial, você não precisará entrar na VM, a menos que precise solucionar problemas. |

1. Escolha a guia **rede** e, em configurar redes virtuais, selecione **criar novo**.

1. Em **Criar rede virtual**, use as configurações na tabela abaixo da imagem:

    ![Guia rede de criar VM](./media/functions-create-vnet/create-vm-2.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | Use o nome padrão gerado para a rede virtual. |
    | **Intervalo de endereços** | 10.10.0.0/16 | Use um só intervalo de endereços para a rede virtual. |
    | **Nome da sub-rede** | Tutorial-Net | Nome da sub-rede. |
    | **Intervalo de endereços** (sub-rede) | 10.10.1.0/24   | O tamanho da sub-rede define o número de interfaces que podem ser adicionadas à sub-rede. Essa sub-rede é usada pelo site do WordPress.  Uma `/24` sub-rede fornece endereços de host de 254. |

1. Selecione **OK** para criar a rede virtual.

1. De volta à guia **rede** , escolha **nenhum** para **IP público**.

1. Escolha a guia **Gerenciamento** e, em **conta de armazenamento de diagnóstico**, escolha a conta de armazenamento que você criou com seu aplicativo de funções.

1. Selecione **Examinar + criar**. Depois de concluir a validação, selecione **Criar**. O processo de criação da VM leva alguns minutos. A VM criada só pode acessar a rede virtual.

1. Depois que a VM for criada, escolha **ir para o recurso** para exibir a página de sua nova VM e, em seguida, escolha **rede** em **configurações**.

1. Verifique se não há um **IP público**. Anote o **IP privado**, que você usa para se conectar à VM do seu aplicativo de funções.

    ![Configurações de rede na VM](./media/functions-create-vnet/vm-networking.png)

Agora você tem um site do WordPress implantado inteiramente em sua rede virtual. Esse site não pode ser acessado pela Internet pública.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Conectar seu aplicativo de funções à rede virtual

Com um site do WordPress em execução em uma VM em uma rede virtual, agora você pode conectar seu aplicativo de funções a essa rede virtual.

1. Em seu novo aplicativo de funções, selecione **rede** no menu à esquerda.

1. Em **Integração VNET**, selecione **Clique aqui para configurar**.

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="Escolher rede no aplicativo de funções":::

1. Na página **integração VNET** , selecione **Adicionar VNET**.

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="Adicionar a visualização de integração de VNet":::

1. Em **status do recurso de rede**, use as configurações na tabela abaixo da imagem:

    ![Definir a rede virtual do aplicativo de funções](./media/functions-create-vnet/networking-3.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Rede Virtual** | MyResource-vnet | Essa rede virtual é aquela que você criou anteriormente. |
    | **Sub-rede** | Criar nova sub-rede | Crie uma sub-rede na rede virtual para uso do seu aplicativo de funções. A integração VNet deve ser configurada para usar uma sub-rede vazia. Não importa que suas funções usem uma sub-rede diferente da VM. A rede virtual roteia automaticamente o tráfego entre as duas sub-redes. |
    | **Nome da sub-rede** | Function-Net | Nome da nova sub-rede. |
    | **Bloco de endereço de rede virtual** | 10.10.0.0/16 | Escolha o mesmo bloco de endereço usado pelo site do WordPress. Você deve ter apenas um bloco de endereço definido. |
    | **Intervalo de endereços** | 10.10.2.0/24   | O tamanho da sub-rede restringe o número total de instâncias para as quais seu aplicativo de funções de plano Premium pode ser expandido. Este exemplo usa uma `/24` sub-rede com 254 endereços de host disponíveis. Essa sub-rede está excessivamente provisionada, mas fácil de calcular. |

1. Selecione **OK** para adicionar a sub-rede. Feche as páginas **integração VNet** e **status do recurso de rede** para retornar à página do aplicativo de funções.

O aplicativo de funções agora pode acessar a rede virtual onde o site do WordPress está em execução. Em seguida, você usa [proxies do Azure Functions](functions-proxies.md) para retornar um arquivo do site do WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Criar um proxy para acessar os recursos da VM

Com a integração VNet habilitada, você pode criar um proxy em seu aplicativo de funções para encaminhar solicitações para a VM em execução na rede virtual.

1. Em seu aplicativo de funções, selecione  **proxies** no menu à esquerda e, em seguida, selecione **Adicionar**. Use as configurações de proxy na tabela abaixo da imagem:

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="Definir as configurações de proxy":::

    | Configuração  | Valor sugerido  | Descrição      |
    | -------- | ---------------- | ---------------- |
    | **Nome** | PlAnT | O nome pode ser qualquer valor. Ele é usado para identificar o proxy. |
    | **Modelo de rota** | /plant | Rota que mapeia para um recurso de VM. |
    | **URL do back-end** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Substitua `<YOUR_VM_IP>` pelo endereço IP da sua VM do WordPress que você criou anteriormente. Esse mapeamento retorna um único arquivo do site. |

1. Selecione **criar** para adicionar o proxy ao seu aplicativo de funções.

## <a name="try-it-out"></a>Experimente

1. No navegador, tente acessar a URL usada como a **URL de back-end**. Conforme esperado, a solicitação atinge o tempo limite. Um tempo limite ocorre porque o site do WordPress está conectado somente à sua rede virtual e não à Internet.

1. Copie o valor da **URL do proxy** do seu novo proxy e cole-o na barra de endereços do seu navegador. A imagem retornada é do site do WordPress em execução dentro de sua rede virtual.

    ![Arquivo de imagem de fábrica retornado do site do WordPress](./media/functions-create-vnet/plant.png)

Seu aplicativo de funções está conectado à Internet e à sua rede virtual. O proxy está recebendo uma solicitação pela Internet pública e, em seguida, agindo como um proxy HTTP simples para encaminhar essa solicitação para a rede virtual conectada. Em seguida, o proxy transmite a resposta de volta para você publicamente pela Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o site do WordPress serve como uma API que é chamada usando um proxy no aplicativo de funções. Esse cenário faz um bom tutorial porque é fácil de configurar e Visualizar. Você pode usar qualquer outra API implantada em uma rede virtual. Você também pode ter criado uma função com código que chama APIs implantadas na rede virtual. Um cenário mais realista é uma função que usa APIs de cliente de dados para chamar uma instância de SQL Server implantada na rede virtual.

As funções em execução em um plano Premium compartilham a mesma infraestrutura de serviço de aplicativo subjacente que aplicativos Web em planos de PremiumV2. Toda a documentação dos [aplicativos Web no serviço Azure app](../app-service/overview.md) se aplica às suas funções de plano Premium.

> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de rede do Functions](./functions-networking-options.md)

[Plano Premium]: functions-premium-plan.md

---
title: Controlar o IP de saída do Azure Functions com um gateway da NAT da Rede Virtual do Azure
description: Um tutorial passo a passo que mostra como configurar a NAT para uma função conectada a uma rede virtual do Azure
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658090"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Tutorial: Controlar o IP de saída do Azure Functions com um gateway da NAT da Rede Virtual do Azure

A NAT (conversão de endereços de rede) de rede virtual simplifica a conectividade com a Internet somente de saída para redes virtuais. Quando configurado em uma sub-rede, toda a conectividade de saída usa seus endereços IP públicos estáticos especificados. Uma NAT pode ser útil para o Azure Functions ou os Aplicativos Web que precisam consumir um serviço de terceiros que usa uma lista de endereços IP permitidos como uma medida de segurança. Para saber mais, confira [O que é a NAT da Rede Virtual?](../virtual-network/nat-overview.md).

Este tutorial mostra como usar as NATs da rede virtual para rotear o tráfego de saída de uma função disparada por HTTP. Essa função permite que você verifique um endereço IP de saída próprio. Durante este tutorial, você vai:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um aplicativo de funções em um plano Premium
> * Criar um endereço IP público
> * Criar um gateway da NAT
> * Configurar o aplicativo de funções para rotear o tráfego de saída pelo gateway da NAT

## <a name="topology"></a>Topologia

O seguinte diagrama mostra a arquitetura da solução que você criará:

![Interface do usuário para integração do gateway da NAT](./media/functions-how-to-use-nat-gateway/topology.png)

As funções em execução no plano Premium têm as mesmas funcionalidades de hospedagem dos aplicativos Web no Serviço de Aplicativo do Azure, que inclui o recurso de Integração VNET. Para saber mais sobre a Integração VNET, incluindo a solução de problemas e a configuração avançada, confira [Integrar seu aplicativo a uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que você tenha noções básicas do endereçamento IP e das sub-redes. Você pode começar com [este artigo que aborda os conceitos básicos de endereçamento e sub-redes](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos outros artigos e vídeos estão disponíveis online.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se você já tiver concluído o tutorial [Integrar o Functions a uma rede virtual do Azure](./functions-create-vnet.md), prossiga para [Criar uma função de gatilho HTTP](#create-function).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**. No Azure Marketplace, selecione **rede** > **rede virtual**.

1. Em **Criar rede virtual**, insira ou selecione as configurações especificadas, conforme mostrado na seguinte tabela:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Nome | Insira *myResourceGroup-vnet*. |
    | Location | Selecione **Leste dos EUA**.|

1. Selecione **Avançar: Endereços IP** e, em **Espaço de endereços IPv4**, insira *10.10.0.0/16*.

1. Escolha **Adicionar sub-rede** e insira *Tutorial-Net* para **Nome da sub-rede** e *10.10.1.0/24* para **Intervalo de endereços de sub-rede**.

    ![Guia Endereços IP para criação de uma VNet](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Selecione **Adicionar** e, em seguida, **Examinar + criar**. Deixe o restante com os valores padrão e selecione **Criar**.

1. Em **Criar rede virtual**, selecione **Criar**.

Em seguida, você criará um aplicativo de funções no [plano Premium](functions-premium-plan.md). Este plano fornece escala sem servidor, dando suporte à integração de rede virtual.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar um aplicativo de funções em um plano Premium

> [!NOTE]  
> Para obter a melhor experiência neste tutorial, escolha .NET como a pilha de runtime e Windows como o sistema operacional. Além disso, crie o aplicativo de funções na mesma região da rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Conectar seu aplicativo de funções à rede virtual

Agora você pode conectar seu aplicativo de funções à rede virtual.

1. No aplicativo de funções, selecione **Rede** no menu à esquerda e, em **Integração VNET**, escolha **Clique aqui para configurar**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Escolher a rede no aplicativo de funções":::

1. Na página **Integração VNET**, selecione **Adicionar VNet**.

1. Em **Status do Recurso de Rede**, use as configurações da tabela abaixo da imagem:

    ![Definir a rede virtual do aplicativo de funções](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Rede Virtual** | MyResourceGroup-vnet | Essa rede virtual é aquela que você criou anteriormente. |
    | **Sub-rede** | Criar uma sub-rede | Crie uma sub-rede na rede virtual para uso do seu aplicativo de funções. A Integração VNET precisa ser configurada para usar uma sub-rede vazia. |
    | **Nome da sub-rede** | Function-Net | Nome da nova sub-rede. |
    | **Bloco de endereço da rede virtual** | 10.10.0.0/16 | Você deve ter apenas um bloco de endereço definido. |
    | **Bloco de endereço da sub-rede** | 10.10.2.0/24   | O tamanho da sub-rede restringe o número total de instâncias para as quais seu aplicativo de funções do plano Premium pode ser expandido. Este exemplo usa uma sub-rede `/24` com 254 endereços de host disponíveis. Essa sub-rede está superprovisionada, mas é fácil de ser calculada. |

1. Selecione **OK** para adicionar a sub-rede. Feche as páginas **Integração VNET** e **Status do Recurso de Rede** para voltar à página do aplicativo de funções.

Agora, o aplicativo de funções pode acessar a rede virtual. Em seguida, você adicionará uma função disparada por HTTP ao aplicativo de funções.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

1. No menu esquerdo da janela **Funções**, selecione **Funções** e depois selecione **Adicionar** no menu superior. 
 
1. Na janela **Nova Função**, selecione **Gatilho HTTP** e aceite o nome padrão para **Nova Função** ou insira um novo nome. 

1. Em **Código + Teste**, substitua o código do script C# gerado pelo modelo (.csx) pelo seguinte código: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Esse código chama um site externo que retorna o endereço IP do chamador que, nesse caso, é essa função. Esse método permite que você determine com facilidade o endereço IP de saída que está sendo usado pelo aplicativo de funções.

Agora você está pronto para executar a função e verificar os IPs de saída atuais.

## <a name="verify-current-outbound-ips"></a>Verificar os IPs de saída atuais

Agora, você pode executar a função. Mas, primeiro, confira o portal e veja quais IPs de saída estão sendo usadas pelo aplicativo de funções.  

1. No aplicativo de funções, selecione **Propriedades** e examine o campo **Endereços IP de Saída**.

    ![Exibir os endereços IP de saída do aplicativo de funções](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Agora, retorne à função de gatilho HTTP, selecione **Código + Teste** e **Testar/Executar**.

    ![Testar a função](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Selecione **Executar** para executar a função e alterne para a **Saída**. 

    ![Testar a saída da função](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Verifique se o endereço IP no corpo da resposta HTTP é um dos valores dos endereços IP de saída exibidos anteriormente.

Agora, você pode criar um IP público e usar um gateway da NAT para modificar esse endereço IP de saída.

## <a name="create-public-ip"></a>Criar um IP público

1. No grupo de recursos, selecione **Adicionar**, procure **Endereço IP público** no Azure Marketplace e escolha **Criar**. Use as configurações da tabela abaixo da imagem:

    ![Criar um endereço IP público](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Configuração      | Valor sugerido  |
    | ------------ | ---------------- |
    | **Versão IP** | IPv4 |
    | **SKU** | Standard |
    | **Camada** | Regional |
    | **Nome** | Outbound-IP |
    | **Assinatura** | Verifique se a sua assinatura é exibida | 
    | **Grupo de recursos** | myResourceGroup (ou o nome atribuído ao grupo de recursos) |
    | **Localidade** | Leste dos EUA (ou a localização que você atribuiu aos outros recursos) |
    | **Zona de Disponibilidade** | Nenhuma Zona |

1. Selecione **Criar** para enviar a implantação.

1. Depois que a implantação for concluída, acesse o recurso de endereço IP público recém-criado e veja o endereço IP na **Visão geral**.

    ![Exibir o endereço IP público](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Criar gateway NAT

Agora, vamos criar o gateway da NAT. Quando você começa com o [tutorial anterior da rede virtual](functions-create-vnet.md), `Function-Net` é o nome sugerido de sub-rede e `MyResourceGroup-vnet` é o nome sugerido de rede virtual nesse tutorial.

1. No grupo de recursos, selecione **Adicionar**, procure **Gateway da NAT** no Azure Marketplace e escolha **Criar**. Use as configurações da tabela abaixo da imagem para preencher a guia **Informações Básicas**:

    ![Criar gateway NAT](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Configuração      | Valor sugerido  |
    | ------------ | ---------------- |  
    | **Assinatura** | Sua assinatura | 
    | **Grupo de recursos** | myResourceGroup (ou o nome atribuído ao grupo de recursos) |
    | **Nome do gateway da NAT** | myNatGateway |
    | **Região** | Leste dos EUA (ou a localização que você atribuiu aos outros recursos) |
    | **Zona de Disponibilidade** | Nenhum |

1. Selecione **Avançar: IP de saída**. No campo **Endereços IP públicos**, selecione o endereço IP público criado anteriormente. Mantenha a opção **Prefixos IP Públicos** desmarcada.

1. Selecione **Avançar: Sub-rede**. Escolha o recurso *myResourceGroup-vnet* no campo **Rede virtual** e na sub-rede *Function-Net*.

    ![Selecionar a sub-rede](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Selecione **Examinar + Criar** e **Criar** para enviar a implantação.

Depois que a implantação for concluída, o gateway da NAT estará pronto para rotear o tráfego da sub-rede do aplicativo de funções para a Internet.

## <a name="update-function-configuration"></a>Atualizar a configuração da função

Agora, você precisa adicionar uma configuração de aplicativo `WEBSITE_VNET_ROUTE_ALL` definida com um valor igual a `1`.  Essa configuração força o tráfego de saída pela rede virtual e pelo gateway da NAT associado. Sem essa configuração, o tráfego da Internet não é roteado pela rede virtual integrada e você verá os mesmos IPs de saída. 

1. Acesse seu aplicativo de funções no portal do Azure e selecione **Configuração** no menu à esquerda.

1. Em **Configurações de aplicativo**, escolha **+ Nova configuração de aplicativo** e conclua e use os seguintes valores para preencher os campos:

    |Nome do campo  |Valor |
    |---|---|
    |**Nome**    |WEBSITE_VNET_ROUTE_ALL|
    |**Valor**   |1|

1. Selecione **OK** para fechar a caixa de diálogo Nova configuração de aplicativo.

1. Escolha **Salvar** e **Continuar** para salvar as configurações.

O aplicativo de funções agora está configurado para rotear o tráfego pela rede virtual associada.

## <a name="verify-new-outbound-ips"></a>Verificar novos IPs de saída

Repita [as etapas anteriores](#verify-current-outbound-ips) para executar a função novamente. Agora, você verá o endereço IP de saída configurado na NAT mostrada na saída da função.

## <a name="clean-up-resources"></a>Limpar recursos

Você criou recursos para concluir este módulo. Esses recursos serão cobrados, dependendo do [status da sua conta](https://azure.microsoft.com/account/) e do [preço do serviço](https://azure.microsoft.com/pricing/). Para evitar gerar custos extras, exclua os recursos quando não precisar mais deles. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Opções de rede do Azure Functions](functions-networking-options.md)

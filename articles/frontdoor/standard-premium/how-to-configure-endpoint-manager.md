---
title: Configurar ponto de extremidade Premium/Standard da porta do Azure com o Gerenciador de ponto de extremidade
description: Este artigo mostra como configurar um ponto de extremidade com o Gerenciador de ponto de extremidade.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098365"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Configurar um ponto de extremidade do Azure front door Standard/Premium (visualização) com o Gerenciador de ponto de extremidade

> [!NOTE]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Exibir os **[documentos de porta frontal do Azure](../front-door-overview.md)**.

Este artigo mostra como criar um ponto de extremidade para um perfil padrão/Premium da porta do Azure existente com o Gerenciador de pontos de extremidade.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um ponto de extremidade padrão/Premium da porta do Azure com o Gerenciador de pontos de extremidade, você deve ter criado pelo menos um perfil de porta de front do Azure criado. O perfil deve ter pelo menos um ou mais pontos de extremidade Standard/Premium de porta frontal do Azure. Para organizar seus pontos de extremidade Standard/Premium de porta frontal do Azure por domínio da Internet, aplicativo Web ou outros critérios, você pode usar vários perfis. 

Para criar um perfil de porta de recepção do Azure, confira [criar um novo perfil padrão/Premium da porta do Azure](create-front-door-portal.md).

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Criar um novo ponto de extremidade padrão/Premium de porta de front-end do Azure

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o perfil Standard/Premium da porta de front-end do Azure.

1. Selecione **Gerenciador de pontos de extremidade**. Em seguida, selecione **Adicionar um ponto de extremidade** para criar um novo ponto de extremidade.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Captura de tela de adicionar um ponto de extremidade pelo Gerenciador de pontos de extremidade.":::

1. Na página **Adicionar um ponto de extremidade** , insira e selecione as configurações a seguir.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Captura de tela de adicionar uma página de ponto de extremidade.":::

    | Configurações | Valor |
    | -------- | ----- |
    | Nome | Insira um nome exclusivo para o novo ponto de extremidade padrão/Premium da porta do Azure. Esse nome é usado para acessar os recursos armazenados em cache no domínio `<endpointname>.az01.azurefd.net` |
    | Tempo limite de resposta de origem (segundos) | Insira um valor de tempo limite em segundos que a porta frontal do Azure aguardará antes de considerar que a conexão com a origem tem tempo limite. |
    | Status | Marque a caixa de seleção para habilitar este ponto de extremidade. |

## <a name="add-domains-origin-group-routes-and-security"></a>Adicionar domínios, grupo de origem, rotas e segurança

1. Selecione **Editar ponto de extremidade** no ponto de extremidade para configurar a rota.

1. Na página **Editar ponto de extremidade** , selecione **+ Adicionar** em domínios.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Captura de tela de selecionar domínio na página Editar ponto de extremidade.":::

### <a name="add-domain"></a>Adicionar domínio

1. Na página **Adicionar domínio** , escolha associar um domínio *do seu perfil de porta de recepção do Azure* ou *Adicionar um novo domínio*. Para obter informações sobre como criar um domínio totalmente novo, consulte [criar um novo domínio personalizado do Azure front door padrão/Premium](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Captura de tela da página Adicionar um domínio.":::

1. Selecione **Adicionar** para adicionar o domínio ao ponto de extremidade atual. O domínio selecionado deve aparecer no painel de domínio.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Captura de tela de domínios na exibição de domínio.":::

### <a name="add-origin-group"></a>Adicionar grupo de origem

1. Selecione **Adicionar** na exibição grupos de origem. A página **Adicionar um grupo de origem** aparece 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Captura de tela da página Adicionar um grupo de origem":::

1. Para **nome**, insira um nome exclusivo para o novo grupo de origem

1. Selecione **Adicionar uma origem** para adicionar uma nova origem ao grupo atual.
 
#### <a name="health-probes"></a>Investigações de integridade
A porta frontal envia solicitações de investigação HTTP/HTTPS periódicas para cada uma de suas origens. As solicitações de investigação determinam a proximidade e a integridade de cada origem para balancear a carga de suas solicitações do usuário final. As configurações de investigação de integridade para um grupo de origem definem como sondar o status de integridade da origem do aplicativo. As configurações a seguir estão disponíveis para a configuração de balanceamento de carga:

> [!WARNING]
> Como a porta frontal tem muitos ambientes de borda globalmente, o volume de investigação de integridade para sua origem pode ser muito alto, variando de 25 solicitações a cada minuto até 1200 solicitações por minuto, dependendo da frequência de investigação de integridade configurada. Com a frequência de investigação padrão de 30 segundos, o volume de investigação em sua origem deve ser de cerca de 200 solicitações por minuto.

* **Status**: especifique se deseja ativar a investigação de integridade. Se você tiver uma única origem em seu grupo de origem, poderá optar por desabilitar as investigações de integridade reduzindo a carga no back-end do aplicativo. Mesmo que você tenha várias origens no grupo, mas apenas uma delas esteja no estado habilitado, você pode desabilitar investigações de integridade.
   
* **Caminho**: a URL usada para solicitações de investigação para toda a origem neste grupo de origem. Por exemplo, se uma das suas origens for contoso-westus.azurewebsites.net e o caminho for definido como/Probe/Test.aspx, os ambientes de porta frontal, supondo que o protocolo esteja definido como HTTP, enviará solicitações de investigação de integridade para `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protocolo**: define se as solicitações de investigação de integridade devem ser enviadas da porta frontal para sua origem com o protocolo http ou HTTPS.
   
* **Método de investigação**: o método http a ser usado para enviar investigações de integridade. As opções incluem GET ou HEAD (padrão).

    > [!NOTE]
    > Para menor carga e custo em sua origem, a porta da frente recomenda o uso de solicitações HEAD para investigações de integridade.

* **Intervalo (em segundos)**: define a frequência de investigações de integridade para sua origem ou os intervalos nos quais cada um dos ambientes de porta frontal envia uma investigação.
   
    >[!NOTE]
    >Para failovers mais rápidos, defina o intervalo para um valor mais baixo. Quanto menor o valor, maior será o volume de investigação de integridade que sua origem receberá. Por exemplo, se o intervalo for definido como 30 segundos com digamos, 100 a porta frontal será exibida globalmente, cada back-end receberá cerca de 200 solicitações de investigação por minuto.

#### <a name="load-balancing"></a>Balanceamento de carga
As configurações de balanceamento de carga para o grupo de origem definem como avaliamos as investigações de integridade. Essas configurações determinam se o back-end está íntegro ou não íntegro. Eles também verificam como balancear a carga do tráfego entre origens diferentes no grupo de origem. As configurações a seguir estão disponíveis para a configuração de balanceamento de carga:

- **Tamanho da amostra**. Identifica quantos exemplos de investigações de integridade precisamos considerar para a avaliação da integridade da origem.

- **Tamanho de amostra bem-sucedido**. Define o tamanho da amostra conforme mencionado anteriormente, o número de amostras bem-sucedidas necessárias para chamar a origem íntegra. Por exemplo, suponha que um intervalo de investigação de integridade de porta frontal seja de 30 segundos, o tamanho da amostra seja 5 e o tamanho de amostra bem-sucedido seja 3. Cada vez que avaliamos as investigações de integridade para sua origem, examinamos os últimos cinco exemplos em 150 segundos (5 x 30). Pelo menos três investigações bem-sucedidas são necessárias para declarar o back-end como íntegro.

- **Sensibilidade de latência (latência extra)**. Define se você deseja que a porta frontal envie a solicitação para a origem dentro do intervalo de sensibilidade de medição de latência ou encaminhe a solicitação para o back-end mais próximo.

Selecione **Adicionar** para adicionar o grupo de origem ao ponto de extremidade atual. O grupo de origem deve aparecer dentro do painel de grupo de origem

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Captura de tela de origens no grupo de origem.":::

### <a name="add-route"></a>Adicionar rota

Selecione **Adicionar** na exibição rotas, a página **Adicionar uma rota** é exibida. Para obter informações sobre como associar o domínio e o grupo de origem, consulte [criar uma nova rota de porta frontal do Azure](how-to-configure-route.md)

### <a name="add-security"></a>Adicionar segurança

1. Selecione **Adicionar** na exibição segurança, a página **Adicionar uma política WAF** aparece
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Captura de tela da página Adicionar uma política de WAF.":::

1. **Política de WAF**: selecione uma política de WAF que você deseja aplicar para o domínio selecionado nesse ponto de extremidade. 
  
   Selecione **criar novo** para criar uma política de WAF totalmente nova.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Captura de tela de criar uma nova política de WAF.":::
   
    **Nome**: Insira um nome exclusivo para a nova política de WAF. Você pode editar essa política com mais configuração na página Firewall do aplicativo Web.

    **Domínios**: selecione o domínio para aplicar a política de WAF.

1. Selecione o botão **Adicionar** . A política WAF deve aparecer dentro do painel de segurança

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Captura de tela da política de WAF no modo de exibição de segurança.":::

## <a name="clean-up-resources"></a>Limpar recursos

Para excluir um ponto de extremidade quando ele não for mais necessário, selecione **excluir ponto de extremidade** no final da linha do ponto de extremidade 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Captura de tela de como excluir um ponto de extremidade.":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre domínios personalizados, continue a [Adicionar um domínio personalizado](how-to-add-custom-domain.md).

---
title: Implantar serviços do Gerenciamento de API do Azure em várias regiões do Azure | Microsoft Docs
description: Saiba como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658119"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure

O Gerenciamento de API do Azure dá suporte à implantação multirregião, o que permite aos editores de API distribuir um único serviço de gerenciamento de API por qualquer número desejado de regiões do Azure. Isso ajuda a reduzir a solicitação de latência percebida pelos consumidores de API distribuídos geograficamente e também melhora a disponibilidade do serviço se uma região ficar offline.

Inicialmente, um novo serviço do Gerenciamento de API do Azure contém apenas uma [unidade][unit] em uma única região do Azure, a Região Primária. Regiões adicionais podem ser facilmente adicionadas por meio do portal do Azure. Um servidor de gateway de gerenciamento de API é implantado em cada região e tráfego de chamada será encaminhado para o gateway mais próximo em termos de latência. Se uma região ficar offline, o tráfego será automaticamente redirecionado para o gateway mais próximo entre os demais.

> [!NOTE]
> O Gerenciamento de API do Azure replica apenas o componente de gateway de API entre regiões. O componente de gerenciamento de serviço é hospedado somente na Região Primária. No caso de uma interrupção na Região Primária, não será possível aplicar alterações de configuração em uma instância do serviço de Gerenciamento de API do Azure - incluindo as configurações ou atualizações de políticas.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Implantar uma instância do serviço de Gerenciamento de API em uma nova região

> [!NOTE]
> Se você ainda não tiver criado uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance].

No portal do Azure, navegue até a página **Escala e preço** da instância do serviço de Gerenciamento de API. 

![Guia Escala][api-management-scale-service]

Para implantar uma nova região, clique em **+ Adicionar região** na barra de ferramentas.

![Adicionar região][api-management-add-region]

Selecione o local na lista suspensa e defina o número de unidades com o controle deslizante.

![Especificar unidades][api-management-select-location-units]

Clique em **Adicionar** para colocar sua seleção na tabela Locais. 

Repita esse processo até que todos os locais estejam configurados e clique em **Salvar** na barra de ferramentas para iniciar o processo de implantação.

## <a name="remove-region"> </a>Excluir uma instância do serviço de Gerenciamento de API de um local

No portal do Azure, navegue até a página **Escala e preço** da instância do serviço de Gerenciamento de API. 

![Guia Escala][api-management-scale-service]

Para o local em que você deseja remover, abra o menu de contexto usando o botão **...** na extremidade direita da tabela. Selecione a opção **Excluir**.

Confirme a exclusão e clique em **Salvar** para aplicar as alterações.

## <a name="route-backend"> </a>Rotear chamadas à API para serviços regionais de back-end

O Gerenciamento de API do Azure apresenta apenas uma URL de serviço de back-end. Mesmo que haja instâncias do Gerenciamento de API do Azure em várias regiões, o gateway de API ainda encaminhará solicitações para o mesmo serviço de back-end, que é implantado em apenas uma região. Nesse caso, o ganho de desempenho será proveniente apenas das respostas armazenadas em cache dentro do Gerenciamento de API do Azure em uma região específica para a solicitação, mas entrar em contato com o back-end em todo o mundo ainda pode causar alta latência.

Para aproveitar totalmente a distribuição geográfica do sistema, você deve ter serviços de back-end implantados nas mesmas regiões como instâncias do Gerenciamento de API do Azure. Então, usando as políticas e a propriedade `@(context.Deployment.Region)`, é possível rotear o tráfego para instâncias locais do back-end.

1. Navegue até a instância do Gerenciamento de API do Azure e clique em **APIs** no menu esquerdo.
2. Selecione a API desejada.
3. Clique em **Editor de códigos** na lista suspensa de seta no **Processamento de entrada**.

    ![Editor de códigos de API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Use a política `set-backend` combinada com as políticas condicionais `choose` para construir uma política de roteamento apropriada na seção `<inbound> </inbound>` do arquivo.

    Por exemplo, o arquivo XML abaixo funcionaria para as regiões Oeste dos EUA e Ásia Oriental:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Você também pode frontal de seus serviços de back-end com [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), direcionar as chamadas à API para o Gerenciador de tráfego e deixá-lo a resolver automaticamente o roteamento.

## <a name="custom-routing"> </a>Usar o roteamento personalizado para gateways regionais do gerenciamento de API

API de gerenciamento encaminha as solicitações para um regionais *gateway* com base em [a menor latência](../traffic-manager/traffic-manager-routing-methods.md#performance). Embora não seja possível substituir essa configuração no gerenciamento de API, você pode usar seu próprio Gerenciador de tráfego com as regras de roteamento personalizadas.

1. Criar seu próprio [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Se você estiver usando um domínio personalizado, [usá-lo com o Gerenciador de tráfego](../traffic-manager/traffic-manager-point-internet-domain.md) em vez do serviço de gerenciamento de API.
1. [Configurar os pontos de extremidade regionais do gerenciamento de API no Gerenciador de tráfego](../traffic-manager/traffic-manager-manage-endpoints.md). Os pontos de extremidade regionais seguem o padrão de URL de `https://<service-name>-<region>-01.regional.azure-api.net`, por exemplo `https://contoso-westus2-01.regional.azure-api.net`.
1. [Configurar os pontos de extremidade de status regional do gerenciamento de API no Gerenciador de tráfego](../traffic-manager/traffic-manager-monitoring.md). Os pontos de extremidade de status regionais seguem o padrão de URL de `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, por exemplo `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Especificar [o método de roteamento](../traffic-manager/traffic-manager-routing-methods.md) do Gerenciador de tráfego.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/

---
title: "Criar uma investigação personalizada - Gateway de Aplicativo do Azure - Portal do Azure | Microsoft Docs"
description: "Saiba como criar uma investigação personalizada para o Gateway de Aplicativo usando o portal"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: bb77c9b39e1aa89f6411de8ec3b1fca41e954bf2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma investigação personalizada para o Gateway de Aplicativo usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, você adiciona uma investigação personalizada a um gateway de aplicativo existente por meio do portal do Azure. As investigações personalizadas são úteis para aplicativos que tenham uma página de verificação de integridade específica ou para aplicativos que não forneçam uma resposta bem-sucedida no aplicativo Web padrão.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não tiver um gateway de aplicativo, visite [Criar um Gateway de Aplicativo](application-gateway-create-gateway-portal.md) a fim de criar um gateway de aplicativo para trabalhar.

## <a name="createprobe"></a>Criar a investigação

As investigações são configuradas em um processo de duas etapas pelo portal. A primeira etapa é criar a investigação. Na segunda etapa, você adiciona a investigação às configurações de HTTP do back-end do Gateway de Aplicativo.

1. Faça logon no [Portal do Azure](https://portal.azure.com). Caso ainda não tenha uma conta, você pode se inscrever para obter uma [avaliação gratuita por um mês](https://azure.microsoft.com/free).

1. No painel Favoritos do portal do Azure, clique em Todos os recursos. Clique no gateway de aplicativo na folha Todos os recursos. Se a assinatura que você selecionou já contém vários recursos, você pode inserir partners.contoso.net na caixa Filtrar por nome... para acessar facilmente o gateway de aplicativo.

1. Clique em **Investigações** e clique no botão **Adicionar** para adicionar uma investigação.

  ![Folha Adicionar Investigação com as informações preenchidas][1]

1. Na folha **Adicionar investigação de integridade**, preencha as informações necessárias para a investigação e ao concluir, clique em **OK**.

  |**Configuração** | **Valor** | **Detalhes**|
  |---|---|---|
  |**Nome**|customProbe|Este valor é um nome amigável para a investigação que está acessível no portal.|
  |**Protocolo**|HTTP ou HTTPS | O protocolo que a investigação de integridade usa.|
  |**Host**|ou seja contoso.com|Este valor é o nome do host usado para a investigação. Aplicável somente quando vários sites são configurados no Gateway de Aplicativo; do contrário, use '127.0.0.1'. Este valor é diferente do nome do host de VM.|
  |**Caminho**|/ ou outro caminho|O restante da URL completa para a investigação personalizada. Um caminho válido começa com "/". Para o caminho padrão de http://contoso.com use apenas '/' |
  |**Intervalo (segundos)**|30|Frequência com que a investigação é executada para verificar a integridade. Não é recomendável defini-la abaixo de 30 segundos.|
  |**Tempo limite (segundos)**|30|A quantidade de tempo que a investigação espera antes de atingir o tempo limite. O intervalo de tempo limite deve ser alto o suficiente para que uma chamada http possa ser feita a fim de garantir que a página de integridade do back-end estará disponível.|
  |**Limite não íntegro**|3|Número de tentativas com falha para ser considerado não íntegro. Um limite de 0 significa que, em caso de falha de verificação de integridade, o back-end é considerado não íntegro imediatamente.|

  > [!IMPORTANT]
  > O nome do host não é o mesmo que o nome do servidor. Esse valor é o nome do host virtual em execução no servidor de aplicativos. A investigação é enviada para http://(nome do host):(porta de httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Adicionar a investigação ao gateway

Agora que a investigação foi criada, é hora de adicioná-la ao gateway. As configurações da investigação são definidas nas configurações de http do back-end do Gateway de Aplicativo.

1. Clique em **Configurações de HTTP** do Gateway de Aplicativo e clique nas configurações de HTTP do back-end atual listadas na janela para abrir a folha de configuração.

  ![janela de configurações de https][2]

1. Na folha de configuração **appGatewayBackEndHttp**, marque a caixa de seleção **Usar investigação personalizada** e selecione a investigação criada na seção [Criar a investigação](#createprobe) do menu suspenso **Investigação personalizada**.
Ao concluir, clique em **Salvar** e as configurações são aplicadas.

A investigação de padrão verifica o acesso padrão ao aplicativo Web. Agora que uma investigação personalizada foi criada, o gateway de aplicativo usa o caminho personalizado definido para monitorar a integridade dos servidores back-end selecionados. Com base nos critérios definidos, o Gateway de Aplicativo verifica o caminho especificado na investigação. Se a chamada para host:Porta/caminho não retornar uma resposta de status HTTP 200-399, o servidor será retirado da rotação após o limite não íntegro ser atingido. A investigação continua na instância não íntegra para determinar quando ela se torna íntegra novamente. Após a instância ser adicionada novamente ao pool de servidores íntegros, o tráfego começa a fluir para ela novamente e a investigação da instância continua normalmente no intervalo especificado pelo usuário.

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar o descarregamento SSL com o Gateway de Aplicativo do Azure, veja [Configurar descarregamento SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png


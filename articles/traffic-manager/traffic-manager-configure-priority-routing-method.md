---
title: Configurar o método de roteamento de tráfego de prioridade usando o Gerenciador de Tráfego do Azure | Microsoft Docs
description: Este artigo explica como configurar o método de roteamento de tráfego de prioridade no Gerenciador de Tráfego
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 66c5bd9390d6fe0f26af66e18aed22c07a7da3e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883979"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Configurar o método de roteamento de tráfego de prioridade no Gerenciador de Tráfego

Independentemente do modo de site, os Sites do Azure já fornecem funcionalidade de failover para sites dentro de um data center (também conhecido como região). O Gerenciador de Tráfego fornece failover para sites em diferentes data centers.

É um padrão comum para failover de serviço é enviar tráfego para um serviço primário e fornecer um conjunto de serviços de backup idênticos para failover. As etapas a seguir explicam como configurar esse failover priorizado com sites e serviços de nuvem do Azure:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Para configurar o método de roteamento de tráfego de prioridade

1. Em um navegador, entre no [Portal do Azure](https://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para obter uma [avaliação gratuita de um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, pesquise os **perfis do Gerenciador de Tráfego** e, em seguida, clique no nome de perfil do qual você deseja configurar o método de roteamento.
3. Na folha **Perfil do Gerenciador de Tráfego**, verifique se os serviços de nuvem e os sites que você deseja incluir na configuração estão presentes.
4. Na seção **Configurações**, clique em **Configuração** e, na folha **Configuração**, preencha da seguinte maneira:
    1. Em **Configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **Prioridade**. Se não for, clique em **Prioridade** na lista suspensa.
    2. Defina as **Configurações do monitor de ponto de extremidade** de forma idêntica para todos os pontos de extremidade nesse perfil, da seguinte maneira:
        1. Selecione o **Protocolo** apropriado e especifique o número da **Porta**. 
        2. Para **Caminho**, digite uma barra "/" */*. Para monitorar os pontos de extremidade, especifique um caminho e um nome de arquivo. A barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).
        3. Na parte superior da página, clique em **Salvar**.
5. Na seção **Configurações**, clique em **Pontos de Extremidade**.
6. Na folha **Pontos de Extremidade**, examine a ordem de prioridade dos pontos de extremidade. Ao selecionar o método de roteamento de tráfego de **Prioridade**, a ordem dos pontos de extremidade selecionados é importante. Verifique a ordem de prioridade dos pontos de extremidade.  O ponto de extremidade primário está no topo. Verifique novamente a ordem em que eles são exibidos. Todas as solicitações serão roteadas para o primeiro ponto de extremidade e, se o Gerenciador de Tráfego o detectar como não íntegro, o tráfego fará failover automaticamente para o próximo ponto de extremidade. 
7. Para alterar a ordem de prioridade do ponto de extremidade, clique no ponto de extremidade e, na folha **Ponto de Extremidade** exibida, clique em **Editar** e altere o valor **Prioridade**, conforme necessário. 
8. Clique em **Salvar** para salvar e alterar as configurações de ponto de extremidade.
9. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
10. Teste as alterações na configuração da seguinte maneira:
    1.  Na barra de pesquisa do portal, pesquise o nome do perfil do Gerenciador de Tráfego e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
    2.  Na folha do perfil do **Gerenciador de Tráfego**, clique em **Visão Geral**.
    3.  A folha do **perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego recém-criado. Isso pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento. Nesse caso, todas as solicitações são roteadas para o primeiro ponto de extremidade e, se o Gerenciador de Tráfego o detectar como não íntegro, o tráfego fará failover automaticamente para o próximo ponto de extremidade.
11. Depois que o perfil do Gerenciador de Tráfego estiver funcionando, edite o registro DNS no servidor DNS autoritativo para que o nome de domínio de sua empresa aponte para o nome de domínio do Gerenciador de Tráfego.

![Configurando o método de roteamento de tráfego de prioridade usando o Gerenciador de Tráfego][1]

## <a name="next-steps"></a>Próximas etapas


- Saiba mais sobre o [método de roteamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md).
- Saiba mais sobre o [método de roteamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
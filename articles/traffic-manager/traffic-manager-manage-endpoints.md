---
title: "Gerenciar pontos de extremidade no Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Este artigo o ajudará a adicionar, remover, habilitar e desabilitar pontos de extremidade do Gerenciador de Tráfego do Azure."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: kumud
ms.openlocfilehash: 765d12bc283d991783fb3190ce7917b573f9fc78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-disable-enable-or-delete-endpoints"></a>Adicionar, desabilitar, habilitar ou excluir pontos de extremidade

O recurso de aplicativos Web no Serviço de Aplicativo do Azure já fornecem failover e funcionalidade de roteamento de tráfego de round robin para sites em um datacenter, independentemente do modo do site. O Gerenciador de Tráfego do Azure permite que você especifique o failover e o roteamento de tráfego para sites e serviços de nuvem em datacenters diferentes. A primeira etapa necessária fornecer essa funcionalidade é adicionar o serviço de nuvem ou ponto de extremidade de site ao Gerenciador de Tráfego.

Você também pode desabilitar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de Tráfego. A desabilitação de um ponto de extremidade o mantém como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Essa ação é útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, ele poderá ser habilitado

> [!NOTE]
> A desabilitação de um ponto de extremidade não tem nada a ver com seu estado de implantação no Azure. Um ponto de extremidade íntegro permanecerá ativo e capaz de receber tráfego mesmo quando for desabilitado no Gerenciador de Tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>Para adicionar um serviço de nuvem ou um ponto de extremidade de serviço de Aplicativo a um perfil do Gerenciador de Tráfego

1. Usando um navegador, entre no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, pesquise o nome do **perfil do Gerenciador de Tráfego** que você deseja modificar e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na folha **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, clique em **Pontos de Extremidade**.
4. Na folha **Pontos de Extremidade** que é exibida, clique em **Adicionar**.
5. Na folha **Adicionar ponto de extremidade**, preencha os seguintes campos:
    1. Para **Tipo**, clique em **Ponto de Extremidade do Azure**.
    2. Forneça um **Nome** pelo qual deseja reconhecer esse ponto de extremidade.
    3. Para o **Tipo de recurso de destino**, na lista suspensa, escolha o tipo de recurso adequado.
    4. Para **Recurso de destino**, na lista suspensa, escolha o recurso de destino adequado para mostrar a listagem de recursos sob a mesma assinatura no **folha de Recursos**. Na folha **Recursos** que é exibida, escolha o serviço que você deseja adicionar como o primeiro ponto de extremidade.
    5. Para **Prioridade**, selecione **1**. Isso faz com que todo o tráfego passe para esse ponto de extremidade, se ele estiver íntegro.
    6. Mantenha a opção **Adicionar como desabilitado** desmarcada.
    7. Clique em **OK**
6.  Repita as etapas 4 e 5 para adicionar o próximo ponto de extremidade do Azure. Certifique-se de adicioná-lo com o valor de **Prioridade** definido como **2**.
7.  Quando a adição de ambos os pontos de extremidade estiver concluída, eles serão exibidos na folha **Perfil do Gerenciador de Tráfego** com seu status de monitoramento como **Online**.

> [!NOTE]
> Depois de adicionar ou remover um ponto de extremidade de um perfil usando o método de roteamento de tráfego *Failover*, a lista de prioridade de failover poderá não ser ordenada da forma desejada. Você pode ajustar a ordem da Lista de Prioridade de Failover na página de Configuração. Para obter mais informações, consulte [Configurar roteamento de tráfego de Failover](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para desabilitar um ponto de extremidade

1. Usando um navegador, entre no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, pesquise o nome do **perfil do Gerenciador de Tráfego** que você deseja modificar e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na folha **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, clique em **Pontos de Extremidade**. 
4. Clique no ponto de extremidade que você deseja desabilitar, e, em seguida, na folha **Ponto de extremidade** que é exibida, clique em **Editar**.
5. Na folha **Ponto de extremidade**, altere o status de ponto de extremidade para **Desabilitado**e, em seguida, clique em **Salvar**.
6. Os clientes continuam a enviar o tráfego para o ponto de extremidade durante o tempo de vida (TTL). Você pode alterar a TTL na página Configuração do perfil do Gerenciador de Tráfego.

## <a name="to-enable-an-endpoint"></a>Para habilitar um ponto de extremidade

1. Usando um navegador, entre no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, pesquise o nome do **perfil do Gerenciador de Tráfego** que você deseja modificar e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na folha **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, clique em **Pontos de Extremidade**. 
4. Clique no ponto de extremidade que você deseja desabilitar, e, em seguida, na folha **Ponto de extremidade** que é exibida, clique em **Editar**.
5. Na folha **Ponto de extremidade**, altere o status de ponto de extremidade para **Habilitado**e, em seguida, clique em **Salvar**.
6. Os clientes continuam a enviar o tráfego para o ponto de extremidade durante o tempo de vida (TTL). Você pode alterar a TTL na página Configuração do perfil do Gerenciador de Tráfego.

## <a name="to-delete-an-endpoint"></a>Como excluir um ponto de extremidade

1. Usando um navegador, entre no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, pesquise o nome do **perfil do Gerenciador de Tráfego** que você deseja modificar e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na folha **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, clique em **Pontos de Extremidade**. 
4. Clique no ponto de extremidade que você deseja desabilitar, e, em seguida, na folha **Ponto de extremidade** que é exibida, clique em **Editar**.
5. Na folha **Ponto de extremidade**, altere o status de ponto de extremidade para **Habilitado**e, em seguida, clique em **Salvar**.


## <a name="next-steps"></a>Próximas etapas

* [Gerenciar perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)
* [Configurar métodos de roteamento](traffic-manager-configure-routing-method.md)
* [Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)
* [Considerações sobre desempenho do Gerenciador de Tráfego](traffic-manager-performance-considerations.md)
* [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)


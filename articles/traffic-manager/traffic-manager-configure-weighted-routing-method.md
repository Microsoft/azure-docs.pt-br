---
title: Configurar o método de roteamento de tráfego round robin ponderado usando o Gerenciador de Tráfego do Azure | Microsoft Docs
description: Este artigo explica como balancear a carga do tráfego usando um método round robin no Gerenciador de Tráfego
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
ms.openlocfilehash: ef39c09d4fc411937fdd6f4b5b5aec491efd0c5f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113280"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Configurar o método de roteamento de tráfego ponderado no Gerenciador de Tráfego

Um padrão comum de roteamento de tráfego é fornecer um conjunto de pontos de extremidade idênticos, que incluem serviços de nuvem e sites, e enviar tráfego a cada um deles com igualdade. As etapas a seguir descrevem como configurar esse tipo de método de roteamento de tráfego.

> [!NOTE]
> O Aplicativo Web do Azure já fornece a funcionalidade de balanceamento de carga round robin para sites em uma Região do Azure (que pode conter vários datacenters). O Gerenciador de Tráfego permite que você distribua o tráfego entre sites em datacenters diferentes.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Como configurar o método de roteamento de tráfego ponderado

1. Usando um navegador, entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para obter uma [avaliação gratuita de um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, pesquise os **perfis do Gerenciador de Tráfego** e, em seguida, clique no nome de perfil do qual você deseja configurar o método de roteamento.
3. Na folha **Perfil do Gerenciador de Tráfego**, verifique se os serviços de nuvem e os sites que você deseja incluir na configuração estão presentes.
4. Na seção **Configurações**, clique em **Configuração** e, na folha **Configuração**, preencha da seguinte maneira:
    1. Para **configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **Ponderado**. Se não for, clique em **Ponderado** na lista suspensa.
    2. Defina as **Configurações do monitor de ponto de extremidade** de forma idêntica para todos os pontos de extremidade nesse perfil, da seguinte maneira:
        1. Selecione o **Protocolo** apropriado e especifique o número da **Porta**. 
        2. Para **Caminho**, digite uma barra "/" */*. Para monitorar os pontos de extremidade, especifique um caminho e um nome de arquivo. A barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).
        3. Na parte superior da página, clique em **Salvar**.
5. Teste as alterações na configuração da seguinte maneira:
    1.  Na barra de pesquisa do portal, pesquise o nome do perfil do Gerenciador de Tráfego e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
    2.  Na folha do perfil do **Gerenciador de Tráfego**, clique em **Visão Geral**.
    3.  A folha **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego recém-criado. Isso pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento. Nesse caso, todas as solicitações são roteadas para cada ponto de extremidade da em round robin.
6. Depois que o perfil do Gerenciador de Tráfego estiver funcionando, edite o registro DNS no servidor DNS autoritativo para que o nome de domínio de sua empresa aponte para o nome de domínio do Gerenciador de Tráfego.

![Configurando o método de roteamento de tráfego ponderado usando o Gerenciador de Tráfego][1]

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [método de roteamento de tráfego de prioridade](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de roteamento de tráfego de desempenho](traffic-manager-configure-performance-routing-method.md).
- Saiba mais sobre o [método de roteamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png

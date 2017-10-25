---
title: "Configurar o método de roteamento de tráfego de desempenho usando o Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Este artigo explica como configurar o Gerenciador de Tráfego para rotear o tráfego para o ponto de extremidade com a menor latência"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 014aa646459cd64fca7c697419324caa3edaeeea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-performance-traffic-routing-method"></a>Configurar o método de roteamento de tráfego de desempenho

O método de roteamento de tráfego de Desempenho permite direcionar o tráfego para o ponto de extremidade com a menor latência de rede do cliente. Normalmente, o data center com a menor latência corresponde ao mais próximo em termos de distância geográfica. Esse método de roteamento de tráfego não consegue considerar as alterações em tempo real à configuração ou à carga de rede.

##  <a name="to-configure-performance-routing-method"></a>Para configurar o método de roteamento de desempenho

1. Em um navegador, entre no [portal do Azure](http://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para obter uma [avaliação gratuita de um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, pesquise os **perfis do Gerenciador de Tráfego** e, em seguida, clique no nome de perfil do qual você deseja configurar o método de roteamento.
3. Na folha **Perfil do Gerenciador de Tráfego**, verifique se os serviços de nuvem e os sites que você deseja incluir na configuração estão presentes.
4. Na seção **Configurações**, clique em **Configuração** e, na folha **Configuração**, preencha da seguinte maneira:
    1. Em **Configurações do método de roteamento de tráfego**, para **Método de roteamento** selecione **Desempenho**.
    2. Defina as **Configurações do monitor de ponto de extremidade** de forma idêntica para todos os pontos de extremidade nesse perfil, da seguinte maneira:
        1. Selecione o **Protocolo** apropriado e especifique o número da **Porta**. 
        2. Para **Caminho**, digite uma barra "/" */*. Para monitorar os pontos de extremidade, especifique um caminho e um nome de arquivo. A barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).
        3. Na parte superior da página, clique em **Salvar**.
5.  Teste as alterações na configuração da seguinte maneira:
    1.  Na barra de pesquisa do portal, pesquise o nome do perfil do Gerenciador de Tráfego e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
    2.  Na folha do perfil do **Gerenciador de Tráfego**, clique em **Visão Geral**.
    3.  A folha do **perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego recém-criado. Isso pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento. Nesse caso, todas as solicitações são roteadas para o ponto de extremidade com a menor latência na rede do cliente.
6. Depois que o perfil do Gerenciador de Tráfego estiver funcionando, edite o registro DNS no servidor DNS autoritativo para que o nome de domínio de sua empresa aponte para o nome de domínio do Gerenciador de Tráfego.

![Configurando o método de roteamento de tráfego de desempenho usando o Gerenciador de Tráfego][1]

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [método de roteamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de roteamento de prioridade](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de roteamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png
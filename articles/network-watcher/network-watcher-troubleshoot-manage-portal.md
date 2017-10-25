---
title: "Solução de problemas de conexões e do gateway de rede virtual do Azure - PowerShell | Microsoft Docs"
description: "Esta página explica como usar o cmdlet do PowerShell para solucionar problemas do Observador de rede do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: c3fa22bd599026b0838b134e26062d9837df703e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Como solucionar problemas de conexões e gateway de rede virtual do usando o PowerShell do Observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

O observador de rede oferece muitos recursos que dizem respeito às noções básicas sobre os recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A solução de problemas de recursos pode ser chamada pelo Portal, pelo PowerShell, pela CLI ou pela API REST. Quando chamado, o Observador de rede inspeciona a integridade de uma conexão ou um gateway de rede virtual e faz um relatório sobre suas descobertas.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

Para obter uma lista de tipos de gateway com suporte, visite [Tipos de Gateway com suporte](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Visão geral

A solução de problemas de recursos fornece a capacidade de solucionar problemas que podem surgir com as conexões e o gateway de rede virtual. Quando uma solução de problemas de recursos recebe uma solicitação, os logs são consultados e inspecionadas. Quando a inspeção estiver concluída, você receberá um relatório com os resultados. As solicitações da solução de problemas de recursos são solicitações de execução longa e podem demorar para gerar um relatório. Os logs de solução de problemas são armazenados em um contêiner em uma conta de armazenamento especificada.

## <a name="troubleshoot-a-gateway-or-connection"></a>Solucionar problemas de um gateway ou conexão

1. Navegue até o [Portal do Azure](https://portal.azure.com) e clique em **Rede** > **Observador de Rede** > **Diagnósticos de VPN**
2. Selecione uma **Assinatura**, **Grupo de Recursos** e **Local**.
3. A solução de problemas de recurso retorna dados sobre a integridade do recurso. Ela também salva os logs relevantes para uma conta de armazenamento a ser examinada. Clique em **Conta de Armazenamento** para selecionar uma conta de armazenamento.
4. Na folha **Configurações da conta**, selecione uma conta de armazenamento existente ou clique em **+ Conta de armazenamento** para criar uma nova.
5. Na folha **Contêineres**, escolha um contêiner existente ou clique em **+ Contêiner** para criar um novo contêiner. Quando concluído, clique em **Selecionar**
6. Selecione os recursos de Gateway e de Conexão para solucionar problemas e clique em **Iniciar Solução de Problemas**

Se vários recursos forem selecionados, a solução de problemas será executada simultaneamente em recursos de gateway. Ela não pode ser executada em uma conexão e no respectivo gateway associado simultaneamente. É recomendável solucionar problemas de gateways primeiro, pois a solução de problemas de conexão é um processo mais longo. Enquanto o diagnóstico de VPN estiver em execução em um recurso, a coluna **STATUS DE SOLUÇÃO DE PROBLEMAS** mostrará um status de **Em execução**

Quando concluído, a coluna de status é alterada para **Íntegro** ou **Não íntegro**.

![Solução de problemas concluída][2]

## <a name="understanding-the-results"></a>Como entender os resultados

Na seção **Detalhes** da janela, a guia **Status** mostra o status da última solução de problemas executada no recurso selecionado. Os resultados do diagnóstico mais recente são mostrados xx minutos após a última execução.

|Propriedade  |Descrição  |
|---------|---------|
|Recurso     | Um link para o recurso.        |
|Caminho de armazenamento     |  Caminho para a conta de armazenamento e o contêiner que contêm os logs (se algum foi produzido durante a execução). Essa configuração não persiste depois de você sair do portal.        |
|Resumo     | Resumo da integridade do recurso.        |
|Detalhes     | Informações detalhadas sobre a integridade do recurso.        |
|Última execução     | A hora em que a solução de problemas foi executada pela última vez.        |


A guia **Ação** fornece diretrizes gerais sobre como resolver o problema. Se for possível executar uma ação para solucionar o problema, você receberá um link com orientações adicionais. Nos casos em que não há orientações adicionais, a resposta fornecerá a url para abrir um caso de suporte.  Para obter mais informações sobre as propriedades da resposta e o do que está incluído, acesse [Visão geral da solução de problemas do observador de rede](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>Próximas etapas

Se as configurações para a conectividade VPN foram alteradas, confira [Gerenciamento de grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para acompanhar quais são as regras de segurança e o grupo de segurança de rede envolvidos na questão.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png

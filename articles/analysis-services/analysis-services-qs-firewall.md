---
title: Início Rápido – Configurar o firewall para servidor do Azure Analysis Services | Microsoft Docs
description: Este início rápido ajuda você a configurar um firewall para o servidor do Azure Analysis Services usando o portal do Azure.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/12/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom:
- mode-portal
ms.openlocfilehash: dd8324607667c6d1f5e397a3266026cf7f2dcc93
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536727"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Início Rápido: Configurar firewall do servidor – Portal

Este guia de início rápido ajuda você a configurar um firewall para o servidor Azure Analysis Services. Habilitar um firewall e configurar intervalos de endereços IP para somente os computadores que acessam o servidor são uma parte importante para proteger o servidor e os dados.

## <a name="prerequisites"></a>Pré-requisitos

- Um servidor do Analysis Services na sua assinatura. Para obter mais informações, confira [Início Rápido: Criar um servidor – Portal](analysis-services-create-server.md) ou [Início Rápido: Criar um servidor – PowerShell](analysis-services-create-powershell.md)
- Um ou mais intervalos de endereços IP para computadores cliente (se necessário).

> [!NOTE]
> As conexões de importação de dados (atualização) e de relatório paginado do Power BI Premium no Microsoft Cloud Alemanha não têm suporte atualmente quando um firewall está habilitado, mesmo quando a configuração Permitir acesso do Power BI está definida como Ativada.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure 

[Entre no portal](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Configure um firewall

1. Clique no seu servidor para abrir a página de Visão geral. 
2. Em **CONFIGURAÇÕES** > **Firewall** > **Habilitar firewall**, selecione **Ativar**.
3. Para habilitar conexões do Power BI e do Power BI Premium, em **Permitir acesso do Power BI**, selecione **Ativar**.  
4. (Opcional) Especifique um ou mais intervalos de endereços IP. Insira um nome, um endereço IP inicial e um final para cada intervalo. O nome da regra de firewall deve ser limitado a 128 caracteres e somente pode conter caracteres maiúsculos, minúsculos, números, sublinhado e hífen. Espaços em branco e outros caracteres especiais não são permitidos.
5. Clique em **Salvar**.

     ![Configurações de firewall](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua os intervalos de endereços IP ou desabilite o firewall.

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido você aprendeu a configurar um firewall para o seu servidor. Agora que você tem o servidor, e ele está protegido com um firewall, você pode adicionar um modelo de dados de exemplo básico a ele a partir do portal. Ter um modelo de exemplo é útil para saber mais sobre como configurar funções do modelo de banco de dados e testar conexões de cliente. Para saber mais, continue para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao seu servidor](analysis-services-create-sample-model.md)

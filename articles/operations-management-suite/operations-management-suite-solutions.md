---
title: "Soluções no OMS (Operations Management Suite) | Microsoft Docs"
description: "As soluções de estendem a funcionalidade do OMS (Operations Management Suite), fornecendo cenários de gerenciamento de pacotes que os clientes podem adicionar ao seu espaço de trabalho do OMS.  Este artigo fornece detalhes sobre soluções personalizadas criadas por clientes e parceiros."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2443dd73fdf441721bd6f6f340da515d9f5a22a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-management-solutions-in-operations-management-suite-oms-preview"></a>Trabalhando com soluções de gerenciamento no OMS (Operations Management Suite) (Versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para soluções de gerenciamento no OMS, que estão atualmente em visualização.    
> 
> 

As soluções de gerenciamento estendem a funcionalidade do OMS (Operations Management Suite), fornecendo cenários de gerenciamento empacotados que os clientes podem adicionar ao seu ambiente.  Além [soluções fornecidas pela Microsoft](../log-analytics/log-analytics-add-solutions.md), parceiros e clientes podem criar soluções de gerenciamento para serem usadas em seu próprio ambiente ou disponibilizadas para os clientes por meio da comunidade.

## <a name="finding-and-installing-management-solutions"></a>Localizando e instalando soluções de gerenciamento
Há vários métodos para localizar e instalar as soluções de gerenciamento, conforme descrito nas seções a seguir.

### <a name="azure-marketplace"></a>Azure Marketplace
Soluções de gerenciamento fornecidos pela Microsoft e parceiros confiáveis podem ser instaladas no Azure Marketplace no portal do Azure.

1. Faça logon no Portal do Azure.
2. No painel esquerdo, selecione **Mais serviços**.
3. Role para baixo até **soluções** ou digite *soluções* na caixa de diálogo **Filtrar**.
4. Clique no botão **+ Adicionar**.
5. Procure soluções nas quais você está interessado ao navegar, clicar no botão **Filtrar** ou digitar na caixa **Pesquisar Tudo**.
6. Clique em um item do Marketplace para exibir as informações detalhadas.
7. Clique em **Criar** para abrir o painel **Adicionar solução**.
8. Você será solicitado a fornecer informações necessárias, como o [Espaço de trabalho OMS e a Conta de automação](#oms-workspace-and-automation-account), além dos valores para quaisquer parâmetros na solução.
9. Clique em **Criar** para instalar a solução.

### <a name="oms-portal"></a>Portal do OMS
Soluções de gerenciamento fornecidas pela Microsoft podem ser instaladas da Galeria de Soluções no portal do OMS.

1. Entre no portal do OMS.
2. Clique no bloco **Galeria de Soluções**.
3. Na página Galeria de Soluções do OMS, saiba mais sobre cada solução disponível. Clique no nome da solução que deseja adicionar ao OMS.
4. Na página para a solução que você escolheu, são exibidas informações detalhadas sobre a solução. Clique em **Adicionar**.
5. Um novo bloco para a solução que você adicionou é mostrado na página Visão geral no OMS e será possível começar a usá-lo depois que o serviço de OMS processar seus dados.

### <a name="azure-quickstart-templates"></a>Modelos de início rápido do Azure
Os membros da comunidade podem enviar soluções de gerenciamento para Modelos de Início Rápido do Azure.  Você pode baixar esses modelos para instalação posterior ou inspecioná-los para saber como [criar suas próprias soluções](#creating-a-solution).

1. Siga o processo descrito no [Espaço de trabalho OMS e na Conta de automação](#oms-workspace-and-automation-account) para vincular um espaço de trabalho e uma conta.
2. Acesse os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).  
3. Pesquise uma solução na qual você esteja interessado.
4. Selecione a solução nos resultados para exibir seus detalhes.
5. Clique no botão **Implantar no Azure**.
6. Você será solicitado a fornecer informações como o grupo de recursos e a localização, além de valores para parâmetros na solução.
7. Clique em **Comprar** para instalar a solução.

### <a name="deploy-azure-resource-manager-template"></a>Implantar o modelo do Azure Resource Manager
Soluções que você obtêm da comunidade ou que você [cria por conta própria](#creating-a-solution) são implementadas como um modelo do Resource Manager e você pode usar qualquer um dos métodos padrão para [implantar um modelo](../azure-resource-manager/resource-group-template-deploy-portal.md).  Observe que, antes de instalar a solução, você deve criar e vincular o [Espaço de trabalho do OMS e a conta de automação](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Espaço de trabalho do OMS e Conta de automação
A maioria das soluções de gerenciamento exige que um [espaço de trabalho do OMS](../log-analytics/log-analytics-manage-access.md) contenha modos de exibição e que uma [Conta de automação](../automation/automation-security-overview.md#automation-account-overview) contenha runbooks e recursos relacionados. O espaço de trabalho e a conta devem atender aos seguintes requisitos.

* Uma solução só pode usar um espaço de trabalho do OMS e uma conta de automação.  
* O espaço de trabalho do OMS e a conta de automação usados por uma solução devem ser vinculados uns aos outros. Um espaço de trabalho do OMS só pode ser vinculado a uma conta de automação e uma conta de automação só pode ser vinculada a um espaço de trabalho do OMS.
* Para ser vinculado, o espaço de trabalho do OMS e a conta de automação devem estar no mesmo grupo de recursos e região.  A exceção é um espaço de trabalho do OMS na região Leste dos EUA e a conta de automação no Leste dos EUA 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Criar um vínculo entre um espaço de trabalho do OMS e a conta de automação
Como você especifica o espaço de trabalho do OMS e conta de automação depende do método de instalação para sua solução.

* Quando você instala uma solução da Microsoft por meio do portal do OMS, ela é instalado no espaço de trabalho atual do OMS e nenhuma conta de automação é necessária.
* Quando você instala uma solução por meio do Marketplace do Azure, você é solicitado a informar um espaço de trabalho do OMS e uma conta de automação, e o vínculo entre eles é criado para você.  
* Para soluções fora do Azure Marketplace, você deve vincular o espaço de trabalho do OMS e a conta de automação antes de instalar a solução.  Você pode fazer isso selecionando qualquer solução no Azure Marketplace e selecionando o espaço de trabalho do OMS e a conta de automação.  Você não precisa instalar a solução em si porque o link será criado assim que o espaço de trabalho do OMS e a conta de automação forem selecionados.  Depois que o link é criado, você pode usar esse espaço de trabalho do OMS e a conta de automação para qualquer solução. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Verificando o link entre um espaço de trabalho do OMS e a Conta de automação
Você pode verificar o link entre um espaço de trabalho do OMS e uma conta de automação usando o procedimento a seguir.

1. Selecione a conta de automação no portal do Azure.
2. Role até a parte inferior do painel **Configurações**.
3. Se houver uma seção chamada **Recursos do OMS** no painel **Configurações**, essa conta será anexada a um espaço de trabalho do OMS.
4. Selecione **Espaço de trabalho** para exibir os detalhes do espaço de trabalho do OMS vinculado a esta conta de automação.

## <a name="listing-management-solutions"></a>Listando as soluções de gerenciamento
Use o procedimento a seguir para exibir as soluções de gerenciamento em espaços de trabalho vinculados à sua assinatura do Azure.

1. Faça logon no Portal do Azure.
2. No painel esquerdo, selecione **Mais serviços**.
3. Role para baixo até **soluções** ou digite *soluções* na caixa de diálogo **Filtrar**.
4. Soluções instaladas em todos os seus espaços de trabalho serão listadas.

Observe que você pode exibir apenas as soluções da Microsoft instaladas no espaço de trabalho atual usando o portal do OMS.

## <a name="removing-a-management-solution"></a>Removendo uma solução de gerenciamento
Quando uma solução de gerenciamento é removida, todos os recursos da solução também são removidos.  

1. Localize a solução no portal do Azure usando o procedimento em [Listando soluções](#listing-solutions).
2. Selecione a solução que você deseja remover.
3. Clique no botão **Excluir** .

## <a name="creating-a-management-solution"></a>Criando uma solução de gerenciamento
Diretrizes completas sobre como criar soluções de gerenciamento estão disponíveis em [Criando soluções no OMS (Operations Management Suite)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Próximas etapas
* Pesquise entre os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates) para obter exemplos de diferentes modelos do Resource Manager.
* Crie suas próprias [soluções de gerenciamento](operations-management-suite-solutions-creating.md).


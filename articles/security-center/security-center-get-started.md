---
title: Início rápido da Central de Segurança do Azure – integrar a assinatura do Azure à Central de Segurança Standard | Microsoft Docs
description: Este guia de início rápido mostra como atualizar para o tipo de preço Standard doa Central de Segurança para obter mais segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: 4464796e655cefd28f858cb732ff3d5721a7aa47
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206808"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Início Rápido: Integrar sua assinatura do Azure à Central de Segurança Standard
A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção contra ameaças nas cargas de trabalho da sua nuvem híbrida. Enquanto a camada Gratuita oferece segurança limitada para somente os recursos do Azure, a camada Standard estende esses recursos para o local e outras nuvens. A Central de Segurança Standard ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controles de acesso e de aplicativo para bloquear atividades mal-intencionadas, detectar ameaças usando a análise e inteligência e responder rapidamente quando sob ataque. Você pode experimentar a Central de Segurança Standard sem nenhum custo. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Neste artigo, você fará um upgrade para a camada Standard para aumentar a segurança e instalará o Microsoft Monitoring Agent em suas máquinas virtuais para monitorar as ameaças e vulnerabilidades de segurança.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, pode se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para fazer o upgrade de uma assinatura para a camada Standard, a você deve ser atribuída a função de Proprietário da assinatura, Colaborador da assinatura ou Administrador de segurança.

## <a name="enable-your-azure-subscription"></a>Habilitar sua assinatura do Azure

1. Faça logon no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Central de Segurança**. **Central de Segurança - Visão geral** é aberto.

   ![Visão geral da Central de Segurança][2]

**Central de Segurança – Visão geral** fornece uma exibição unificada sobre a postura de segurança das cargas de trabalho da sua nuvem híbrida, permitindo que você descubra e avalie a segurança de suas cargas de trabalho e identifique e reduza riscos. A Central de Segurança habilita automaticamente qualquer uma das suas assinaturas do Azure que não estavam incorporadas por você ou por outro usuário da assinatura à camada Gratuita.

Você pode visualizar e filtrar a lista de assinaturas clicando no item do menu **Assinaturas**. A Central de Segurança agora iniciará a avaliar a segurança dessas assinaturas para identificar vulnerabilidades de segurança. Para personalizar os tipos de avaliações, você pode modificar a política de segurança. Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir a conformidade com requisitos de regulamentação de segurança ou da empresa.

Em poucos minutos após iniciar a Central de Segurança pela primeira vez, você poderá ver:

- **Recomendações** de maneiras para melhorar a segurança de suas assinaturas do Azure. Clique no bloco **Recomendações** para iniciar uma lista priorizada.
- Um inventário de recursos de **Computação e aplicativos**, **Rede**, **Segurança de dados**, e **Identidade e acesso** que estão sendo avaliados agora pela Central de Segurança junto com a postura de segurança de cada um.

Para tirar total proveito da Central de Segurança, você precisa concluir as etapas abaixo para fazer upgrade para a camada Standard e instalar o Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Atualizar para a camada Standard
Para os guias de início rápido e tutoriais da Central de Segurança, você deve atualizar para a camada Standard. Há uma avaliação gratuita da Central de Segurança Standard. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). 

1. No menu principal da Central de Segurança, selecione **Introdução**.
 
   ![Introdução][4]

2. Em **Atualização**, a Central de Segurança lista as assinaturas e os workspaces qualificados para a integração. 
   - Você pode clicar em **Aplicar sua avaliação** expansível para ver uma lista de todas as assinaturas e workspaces com seu status de qualificação de avaliação.
   -    Você pode atualizar as assinaturas e os workspaces que não são qualificados para avaliação.
   -    Você pode selecionar workspaces e assinaturas qualificados para iniciar sua avaliação.
3. Clique em **Iniciar avaliação** para iniciar sua avaliação das assinaturas selecionadas.


  ![Alertas de segurança][9]

## <a name="automate-data-collection"></a>Automatizar a coleta de dados
A Central de Segurança coleta dados de suas VMs do Azure e dos computadores não Azure a fim de monitorar as ameaças e vulnerabilidades de segurança. Os dados são coletados usando o Microsoft Monitoring Agent, que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados em seu workspace para serem analisados. Por padrão, a Central de Segurança criará um novo workspace para você.

Quando o provisionamento automático está habilitado, a Central de Segurança instala o Microsoft Monitoring Agent em todas as VMs do Azure com suporte, bem como em quaisquer novas VMs que forem criadas. O provisionamento automático é altamente recomendável.

Para habilitar o provisionamento automático do Microsoft Monitoring Agent:

1. No menu principal da Central de Segurança, selecione **Política de Segurança**.
2. Na linha da assinatura, selecione **Editar configurações>** .
3. Na guia **Coleta de Dados**, defina **Provisionamento automático** como **Ativado**.
4. Clique em **Salvar**.
---
  ![Habilitar o provisionamento automático][6]

Com essa nova percepção de suas VMs do Azure, a Central de Segurança pode fornecer Recomendações adicionais relacionadas ao status de atualização do sistema, configurações de segurança do SO, proteção de ponto de extremidade, além de gerar alertas de Segurança adicionais.

  ![Recomendações][8]

## <a name="clean-up-resources"></a>Limpar recursos
Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e os guias de início rápido subsequentes, continue executando a camada Standard e mantenha o provisionamento automático habilitado. Se você não planejar continuar ou quiser retornar para a camada Gratuita:

1. Retorne ao menu principal da Central de Segurança e selecione a **Política de segurança**.
2. Na linha da assinatura que você deseja retornar para gratuita, selecione **Editar configurações>** .
3. Selecione **Tipo de preço** e selecione **Gratuito** para alterar a assinatura da camada Standard para a camada Gratuita.
5. Clique em **Salvar**.

Se quiser desabilitar o provisionamento automático:

1. Retorne ao menu principal da Central de Segurança e selecione **Política de segurança**.
2. Na linha da assinatura em que você deseja desabilitar o provisionamento automático, selecione **Editar configurações>** .
3. Na guia **Coleta de Dados**, defina **Provisionamento automático** como **Desativado**.
4. Clique em **Salvar**.

>[!NOTE]
> Desabilitar o provisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure em que o agente tenha sido provisionado. Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos.
>

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você fez upgrade para camada Standard e provisionou o Microsoft Monitoring Agent para o gerenciamento unificado de segurança e proteção contra ameaças em suas cargas de trabalho de nuvem híbrida. Para saber mais sobre como usar a Central de Segurança, continue para o guia de início rápido para a integração de computadores com Windows locais e em outras nuvens.

> [!div class="nextstepaction"]
> [Início Rápido: Integrar computadores Windows à Central de Segurança do Azure](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png

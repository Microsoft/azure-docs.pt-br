---
title: Integrar soluções de segurança na Central de Segurança do Azure | Microsoft Docs
description: Saiba como a Central de Segurança do Azure se integra aos parceiros para aprimorar a segurança geral dos recursos do Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: ff23a1fa4b631fc10163f22d94ccdbd8cbe657c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099243"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança na Central de Segurança do Azure
Este documento ajuda você a gerenciar soluções de segurança já conectadas à Central de Segurança do Azure e a adicionar novas.

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança integradas do Azure
A Central de Segurança facilita a criação de soluções de segurança integradas no Azure. Os benefícios incluem:

- **Implantação simplificada**: a Central de Segurança oferece provisionamento simplificado das soluções integradas de parceiros. Para soluções como antimalware e avaliação de vulnerabilidade, a central de segurança pode provisionar o agente em suas máquinas virtuais. Para dispositivos de firewall, a central de segurança pode cuidar de grande parte da configuração de rede necessária.
- **Detecções integradas**: os eventos de segurança das soluções de parceiros são coletados, agregados e exibidos automaticamente como parte dos alertas e incidentes da central de segurança. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Unificação de gerenciamento e monitoramento de integridade**: os clientes podem usar eventos de integridade integrados para monitorar todas as soluções de parceiro em um relance. O gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.

Atualmente, as soluções de segurança integradas incluem a avaliação de vulnerabilidade por [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e [Microsoft Azure o Firewall do aplicativo Web no gateway aplicativo Azure](../web-application-firewall/ag/ag-overview.md).

> [!NOTE]
> A central de segurança não instala o agente de Log Analytics em dispositivos virtuais de parceiros, pois a maioria dos fornecedores de segurança proíbe agentes externos em execução em seus dispositivos.

Para saber mais sobre a integração das ferramentas de verificação de vulnerabilidade do Qualys, incluindo um scanner interno disponível para clientes do Azure defender, consulte [avaliações de vulnerabilidade para suas máquinas virtuais do Azure](deploy-vulnerability-assessment-vm.md).

A Central de Segurança também oferece análise de vulnerabilidades para:

* Bancos de dados SQL - confira [Explorar relatórios de avaliação de vulnerabilidades no painel de avaliação de vulnerabilidades](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)
* Imagens do registro de contêiner do Azure-consulte [usar o Azure defender para registros de contêiner para verificar se há vulnerabilidades em suas imagens](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Como as soluções de segurança são integradas
As soluções de segurança do Azure implantadas da Central de Segurança serão conectadas automaticamente. Você também pode conectar outras fontes de dados de segurança, incluindo computadores executados localmente ou em outras nuvens.

[![Integração de soluções de parceiros](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerenciar soluções de segurança integrada do Azure e outras fontes de dados

1. No [portal do Azure](https://azure.microsoft.com/features/azure-portal/), abra a **Central de Segurança**.

1. No menu da central de segurança, selecione **soluções de segurança**.

Na página **soluções de segurança** , você pode ver a integridade das soluções de segurança integradas do Azure e executar tarefas básicas de gerenciamento.

### <a name="connected-solutions"></a>Soluções conectadas

A seção **soluções conectadas** inclui soluções de segurança que estão atualmente conectadas à central de segurança. Ele também mostra o status de integridade de cada solução.  

![Soluções conectadas](./media/security-center-partner-integration/connected-solutions.png)

O status de uma solução de parceiro pode ser:

* **Íntegro** (verde)-sem problemas de integridade.
* Não **íntegro** (vermelho) – há um problema de integridade que requer atenção imediata.
* **Relatório interrompido** (laranja)-a solução parou de relatar sua integridade.
* **Não relatado** (cinza)-a solução ainda não relatou nada e nenhum dado de integridade está disponível. O status de uma solução poderá não ser relatado se ele tiver sido conectado recentemente e ainda estiver sendo implantado.

> [!NOTE]
> Se os dados de status de integridade não estiverem disponíveis, a Central de Segurança mostrará a data e a hora do último evento recebido para indicar se a solução está reportando ou não. Se nenhum dado de integridade estiver disponível e nenhum alerta for recebido nos últimos 14 dias, a central de segurança indicará que a solução não está íntegra ou não está relatando.
>
>

Selecione **Exibir** para obter informações adicionais e opções, como:

   - **Console de solução** – abre a experiência de gerenciamento para esta solução.
   - **Vincular VM** – abre a página vincular aplicativos. Nela, você pode conectar recursos à solução de parceiro.
   - **Excluir solução**
   - **Configurar**

   ![Detalhes da solução de parceiro](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Soluções descobertas

A central de segurança descobre automaticamente as soluções de segurança em execução no Azure, mas não está conectada à central de segurança e exibe as soluções na seção **soluções descobertas** . Essas soluções incluem soluções do Azure, como [Azure ad Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)e soluções de parceiros.

> [!NOTE]
> Habilite o **Azure defender** no nível de assinatura para o recurso de soluções descobertas. Saiba mais em [início rápido: habilitar o Azure defender](enable-azure-defender.md).

Selecione **conectar** em uma solução para integrar com a central de segurança e ser notificado sobre alertas de segurança.

### <a name="add-data-sources"></a>Adicionar fontes de dados

A seção **Adicionar fontes de dados** inclui outras fontes de dados disponíveis que podem ser conectadas. Para obter instruções sobre como adicionar dados de qualquer uma dessas fontes, clique em **ADICIONAR**.

![Fontes de dados](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a integrar as soluções de parceiro à Central de Segurança. Para saber como configurar uma integração com o Azure Sentinel ou qualquer outro SIEM, consulte [Exportar continuamente os dados da central de segurança](continuous-export.md).
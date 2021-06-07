---
title: Configurar o dimensionamento automático de uma instância do Gerenciamento de API do Azure | Microsoft Docs
description: Este tópico descreve como configurar o comportamento de dimensionamento automático de para uma instância do Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 41a97c639d9148f2bff3e4f530363ea05a65a7b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018233"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Dimensionar automaticamente uma instância do Gerenciamento de API do Azure  

A instância do serviço de Gerenciamento de API do Azure pode ser dimensionada automaticamente com base em um conjunto de regras. Esse comportamento pode ser habilitado e configurado por meio do Azure Monitor e tem suporte apenas nas camadas **Standard** e **Premium** do serviço de Gerenciamento de API do Azure.

O artigo aborda o processo de configuração do dimensionamento automático e sugere a configuração ideal das regras de dimensionamento automático.

> [!NOTE]
> O serviço de gerenciamento de API na camada de **consumo** é dimensionado automaticamente com base no tráfego, sem nenhuma configuração adicional necessária.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas deste artigo, é necessário:

+ Ter uma assinatura ativa do Azure.
+ Ter uma instância do Gerenciamento de API do Azure. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Entender o conceito de [Capacidade de uma instância do Gerenciamento de API do Azure](api-management-capacity.md).
+ Entender o [processo de dimensionamento manual de uma instância do Gerenciamento de API do Azure](upgrade-and-scale.md), incluindo as consequências de custo.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Limitações do dimensionamento automático do Gerenciamento de API do Azure

Certas limitações e consequências das decisões de dimensionamento precisam ser consideradas antes de configurar o comportamento de dimensionamento automático.

+ O dimensionamento automático pode ser habilitado somente para camadas **Standard** e **Premium** do serviço de Gerenciamento de API do Azure.
+ Tipos de preço também especificam o número máximo de unidades para uma instância de serviço.
+ O processo de dimensionamento levará pelo menos 20 minutos.
+ Se o serviço estiver bloqueado por outra operação, a solicitação de dimensionamento falhará e será repetida automaticamente.
+ No caso de um serviço com implantações em várias regiões, somente as unidades no **Local primário** poderão ser dimensionadas. Unidades em outros locais não podem ser dimensionadas.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Habilitar e configurar o dimensionamento automático para o serviço de Gerenciamento de API do Azure

Siga as etapas abaixo para configurar o dimensionamento automático em um serviço de Gerenciamento de API do Azure:

1. Navegue até a instância **Monitor** no portal do Azure.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Selecione **Autoscale** no menu à esquerda.

    ![Recurso de dimensionamento automático do Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Localize o serviço de Gerenciamento de API do Azure com base nos filtros nos menus suspensos.
4. Selecione a instância desejada do Serviço de Gerenciamento de API do Azure.
5. Na seção de abertos recentemente, clique no botão **Habilitar dimensionamento automático**.

    ![Ativar o dimensionamento automático do Azure Monitor](media/api-management-howto-autoscale/03.png)

6. Na seção **Regras**, clique em **+ Adicionar uma regra**.

    ![Adicionar regra de dimensionamento automático do Azure Monitor](media/api-management-howto-autoscale/04.png)

7. Defina uma nova regra de escala horizontal.

   Por exemplo, uma regra de escala horizontal pode disparar uma adição de uma unidade do Gerenciamento de API do Azure, quando a métrica de capacidade média nos últimos 30 minutos exceder 80%. A tabela a seguir fornece a configuração para essa regra.

    | Parâmetro             | Valor             | Observações                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem da métrica         | Recurso atual  | Defina a regra com base nas atuais métricas de recursos de Gerenciamento de API do Azure.                                                                                                                                                                                                     |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregação de tempo      | Média           |                                                                                                                                                                                                                                                                                 |
    | Nome da métrica           | Capacity          | Métrica de capacidade é uma métrica do Gerenciamento de API do Azure que reflete o uso de recursos de uma instância do Gerenciamento de API do Azure.                                                                                                                                                            |
    | Estatística de intervalo de agregação  | Média           |                                                                                                                                                                                                                                                                                 |
    | Operador              | Maior que      |                                                                                                                                                                                                                                                                                 |
    | Limite             | 80%               | O limite para a métrica de capacidade média.                                                                                                                                                                                                                                 |
    | Duração (em minutos) | 30                | O intervalo de tempo para a média da métrica de capacidade é específico para padrões de uso. Quanto maior for o período de tempo, mais suave será a reação - picos intermitentes terão menos efeito sobre a decisão de escalar horizontalmente. No entanto, ele também atrasará o gatilho de escala horizontal. |
    | *Ação*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operação             | Aumentar a contagem em |                                                                                                                                                                                                                                                                                 |
    | Contagem de instâncias        | 1                 | Escalar horizontalmente a instância do Gerenciamento de API do Azure por 1 unidade.                                                                                                                                                                                                                          |
    | Tempo de resfriamento (minutos)   | 60                | Leva pelo menos 20 minutos para que o serviço de gerenciamento de API do Azure Escale horizontalmente. Na maioria dos casos, o período de resfriamento de 60 minutos impede que o dispare muitos limites de escalabilidade.                                                                                                  |

8. Clique em **Adicionar** para salvar a regra.

    ![Regra de escala horizontal do Azure Monitor](media/api-management-howto-autoscale/05.png)

9. Clique novamente em **+ Adicionar uma regra**.

    Nesse momento, uma regra de redução horizontal precisa ser definida. Isso garantirá que os recursos não estarão sendo desperdiçados quando o uso das APIs diminuir.

10. Defina uma nova regra de redução horizontal.

    Por exemplo, uma regra de redução horizontal pode disparar uma remoção de uma unidade do Gerenciamento de API do Azure, quando a métrica de capacidade média nos últimos 30 minutos for menor que 35%. A tabela a seguir fornece a configuração para essa regra.

    | Parâmetro             | Valor             | Observações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem da métrica         | Recurso atual  | Defina a regra com base nas atuais métricas de recursos de Gerenciamento de API do Azure.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregação de tempo      | Média           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nome da métrica           | Capacity          | Mesma métrica mesma que aquela usada para a regra de escala horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Estatística de intervalo de agregação  | Média           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operador              | Menor que         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Limite             | 35%               | Da mesma forma que para a regra de escala horizontal, esse valor depende em grande parte dos padrões de uso do Gerenciamento de API do Azure. |
    | Duração (em minutos) | 30                | Mesmo valor que aquele usado para a regra de escala horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Ação*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operação             | Diminuir contagem em | Oposta a que foi usada para a regra de escala horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Contagem de instâncias        | 1                 | Mesmo valor que aquele usado para a regra de escala horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Tempo de resfriamento (minutos)   | 90                | A redução horizontal deve ser mais conservadora do que uma escala horizontal, portanto, o período de resfriamento deve ser mais longo.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Clique em **Adicionar** para salvar a regra.

    ![Regra de escala horizontal do Azure Monitor](media/api-management-howto-autoscale/06.png)

12. Defina o número **máximo** de unidades do Gerenciamento de API do Azure.

    > [!NOTE]
    > O Gerenciamento de API do Azure tem um limite de unidades para as quais uma instância pode ser escalada horizontalmente. O limite depende de uma camada de serviço.

    ![Captura de tela que realça onde definir o número máximo de unidades de gerenciamento de API do Azure.](media/api-management-howto-autoscale/07.png)

13. Clique em **Save** (Salvar). O dimensionamento automático foi configurado.

## <a name="next-steps"></a>Próximas etapas

- [Como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure](api-management-howto-deploy-multi-region.md)
- [Otimize e economize em seus gastos com a nuvem](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
---
title: Ver reservas para recursos do Azure
description: Saiba como exibir as Reservas do Azure no portal do Azure. Confira as reservas e a utilização por meio das APIs, do PowerShell, da CLI e do Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 8c69f477f363654b8bd707949f0a5b4c46a4e8df
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561097"
---
# <a name="view-azure-reservations"></a>Exibir reservas do Azure

Este artigo explica como ver as reservas do Azure no portal do Azure. Você pode ver e gerenciar uma reserva comprada no portal do Azure.

## <a name="who-can-manage-a-reservation-by-default"></a>Quem pode gerenciar uma reserva por padrão

Por padrão, os seguintes usuários podem ver e gerenciar reservas:

- A pessoa que compra uma reserva e o administrador da conta da assinatura para cobrança usada para comprar a reserva são adicionadas ao pedido de reserva.
- Administradores de cobrança do Contrato Enterprise e do Contrato de Cliente da Microsoft.

Para permitir que outras pessoas gerenciem reservas, você tem duas opções:

- Delegar o gerenciamento de acesso para um pedido de reserva individual:
    1. Entre no [portal do Azure](https://portal.azure.com).
    1. Selecione **Todos os serviços** > **Reserva** para listar as reservas às quais você tem acesso.
    1. Selecione a reserva que deseja delegar acesso a outros usuários.
    1. Em Detalhes da reserva, selecione o pedido de reserva.
    1. Selecione **IAM (Controle de acesso)** .
    1. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Se quiser permitir acesso limitado, escolha outra função.
    1. Digite o endereço de email do usuário que você deseja adicionar como proprietário.
    1. Selecione o usuário e, em seguida, selecione **Salvar**.

- Adicione um usuário como Administrador de cobrança a um Contrato Enterprise ou um Contrato de Cliente da Microsoft:
    - Para um Contrato Enterprise, adicione usuários com a função _Administrador Corporativo_ para ver e gerenciar todos os pedidos de reserva que se aplicam ao Contrato Enterprise. Os usuários com a função _Administrador Corporativo (somente leitura)_ só podem ver a reserva. Os administradores de departamento e os proprietários da conta não podem ver as reservas _a menos que_ sejam explicitamente adicionados a elas por meio do Controle de acesso (IAM). Para obter mais informações, confira [Como gerenciar funções corporativas do Azure](../manage/understand-ea-roles.md).

        _Os administradores corporativos podem se apropriar de um pedido de reserva e adicionar outros usuários a uma reserva usando o Controle de acesso (IAM)._
    - Para um Contrato de Cliente da Microsoft, os usuários com a função proprietário ou colaborador do perfil de cobrança podem gerenciar todas as compras de reserva feitas usando o perfil de cobrança. Os leitores do perfil de cobrança e os gerenciadores de faturas podem ver todas as reservas pagas com o perfil de cobrança. No entanto, eles não podem fazer alterações nas reservas.
    Para obter mais informações, confira [Funções e tarefas do perfil de cobrança](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Como os administradores de cobrança veem ou gerenciam as reservas

1. Acesse **Gerenciamento de Custos e Cobrança** e, no lado esquerdo da página, selecione **Transações de Reserva**.
2. Se você tiver as permissões de cobrança necessárias, poderá ver e gerenciar as reservas. Caso não veja nenhuma reserva, verifique se está conectado usando o locatário do Azure AD em que as reservas foram criadas.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Ver a reserva e a utilização no portal do Azure

Para ver uma reserva como Proprietário ou Leitor

1. Entre no [portal do Azure](https://portal.azure.com).
2. Acesse [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. A lista mostra todas as reservas nas quais você tem a função Proprietário ou Leitor. Cada reserva mostra o último percentual de utilização conhecido.
4. Selecione o percentual de utilização para ver o histórico e os detalhes de utilização. Veja os detalhes no vídeo abaixo.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Obtenha as reservas e a utilização usando APIs, o PowerShell e a CLI

Obtenha uma lista de todas as reservas usando os seguintes recursos:

- [API: Pedido de Reserva – Lista](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Pedido de Reserva – Lista](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: Pedido de Reserva – Lista](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Você também pode obter a [utilização de reserva](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) usando a API de uso da Instância Reservada. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Ver reservas e a utilização no Power BI

Há duas opções para os usuários do Power BI
- Pacote de Conteúdo: a data da compra da reserva e os dados e utilização estão disponíveis no [pacote de conteúdo do Consumption Insights para Power BI](/power-bi/desktop-connect-azure-cost-management). Crie os relatórios desejados usando o pacote de conteúdo. 
- Aplicativo de gerenciamento de custos: Use o [Aplicativo de Gerenciamento de Custos](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) para os relatórios pré-criados que você pode personalizar ainda mais.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md).
- [Entender o uso de reserva para seu Registro Enterprise](understand-reserved-instance-usage-ea.md).
- [Entender o uso de reserva para assinaturas do CSP](/partner-center/azure-reservations).


---
title: Transferências do Azure Enterprise
description: Descreve as transferências do EA do Azure
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7aa57fa20c3a043cdb210ccd8a5ddbf61323716d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943688"
---
# <a name="azure-enterprise-transfers"></a>Transferências do Azure Enterprise

Este artigo fornece uma visão geral das transferências do Enterprise.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Transferir uma conta empresarial para um novo registro

Uma transferência de conta move um proprietário da conta de um registro para outro. Todas as assinaturas relacionadas no proprietário da conta serão movidas para o registro de destino. Use uma transferência de conta quando tiver vários registros ativos e só desejar mover os proprietários da conta selecionados.

Esta seção destina-se apenas a fins informativos, pois a ação não pode ser executada por um administrador corporativo. Uma solicitação de suporte é necessária para transferir uma conta empresarial para um novo registro.

Tenha os pontos a seguir em mente ao transferir uma conta empresarial para um novo registro:

- Somente as contas especificadas na solicitação são transferidas. Se todas as contas forem escolhidas, elas serão todas transferidas.
- O registro de origem mantém o status como ativo ou estendido. Você poderá continuar usando o registro até que ele expire.

### <a name="prerequisites"></a>Pré-requisitos

Ao solicitar uma transferência de conta, forneça as seguintes informações:

- O número do registro de destino, o nome da conta e o email do proprietário da conta a ser transferido
- Para o registro de origem, o número de registro e a conta a serem transferidos

Outros pontos para ter em mente antes de uma transferência de conta:

- A aprovação de um administrador de EA é necessária para o registro de origem e o de destino
- Se uma transferência de conta não atender aos seus requisitos, considere uma transferência de registro.
- A transferência de conta transfere todos os serviços e assinaturas relacionados às contas específicas.
- Depois que a transferência for concluída, a conta transferida aparecerá inativa no registro de origem e aparecerá ativa no registro de destino.
- A conta mostra a data de término correspondente à data de transferência efetiva no registro de origem e como uma data de início no registro de destino.
- Qualquer uso ocorrido para a conta antes da data de transferência efetiva permanece no registro de origem.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Transferir Registro Enterprise para um novo registro

Uma transferência de registro é considerada quando:

- O termo do pagamento antecipado de um registro atual chegou ao fim.
- Um registro está no status expirado/estendido e um novo contrato é negociado.
- Você tem vários registros e deseja combinar todas as contas e a cobrança em um só registro.

Esta seção destina-se apenas a fins informativos, pois a ação não pode ser executada por um administrador corporativo. Uma solicitação de suporte é necessária para transferir um Registro Enterprise para um novo, a menos que o registro seja qualificado para a [transferência de registro automática](#auto-enrollment-transfer).

Quando você solicita a transferência de um Registro Enterprise inteiro para um registro, as seguintes ações ocorrem:

- Todos os serviços, as assinaturas, as contas, os departamentos e toda a estrutura de registro do Azure, incluindo todos os administradores de departamento do EA, são transferidos para um novo registro de destino.
- O status do registro é definido como _Transferido_. O registro transferido está disponível apenas para fins de relatório de uso histórico.
- Você não pode adicionar funções nem assinaturas a um registro transferido. O status de transferido impede o uso adicional no registro.
- Qualquer saldo restante do pagamento antecipado do Azure no contrato é perdido, incluindo termos futuros.
-    Se o registro do qual você está fazendo a transferência tiver compras de RI, o valor de compra de RI permanecerá no registro de origem. No entanto, todos os benefícios de RI serão transferidos para uso no novo registro.
-    O valor avulso de compra do marketplace e todos os valores fixos mensais já incorridos no registro antigo não são transferidos para o novo registro. As cobranças do Marketplace baseadas no consumo serão transferidas.

### <a name="effective-transfer-date"></a>Data de início de vigência da transferência

A data de efetivação da transferência pode ser a data de início do registro de destino ou uma data posterior a ela.

O uso do registro de origem é cobrado no pagamento antecipado do Azure ou como excedente. O uso que ocorre após a data de início de vigência da transferência é transferido para o novo registro e cobrado.

### <a name="prerequisites"></a>Pré-requisitos

Ao solicitar uma transferência de registro, forneça as seguintes informações:

- Para o registro de origem, o número de registro.
- Para o registro de destino, o número de registro no qual transferir.
- A data de início de vigência da transferência do registro pode ser uma data igual ou posterior à data de início do registro de destino. A data escolhida não pode afetar o uso de nenhuma fatura excedente já emitida.

Outros pontos para ter em mente antes de uma transferência de registro:

- É necessária a aprovação dos administradores do EA do registro de origem e de destino.
- Se uma transferência de registro não atender aos seus requisitos, considere uma transferência de conta.
- O status do registro de origem será atualizado para transferido e só estará disponível para fins de relatório de uso histórico.

### <a name="auto-enrollment-transfer"></a>Transferência de registro automática

Você poderá ver que um registro tem o estado **Transferido**, mesmo que não tenha enviado um tíquete de suporte para solicitar uma transferência de registro. O estado **Transferido** é resultado do processo de transferência de registro automática. Para que o processo de transferência de registro automática ocorra durante a fase de renovação, há alguns itens que devem ser incluídos no novo contrato:

- Número do registro anterior (precisa existir no portal do EA)
- A data de validade do número do registro anterior é um dia antes da data de início de vigência do novo contrato
- O novo contrato tem uma ordem de pagamento antecipado do Azure faturada que tem uma data atual ou retroativa
- O novo registro é criado no portal do EA

Se não houver dados de uso ausentes no portal do EA entre o registro anterior e o novo registro, você não precisará criar um tíquete de suporte de transferência.

### <a name="azure-prepayment"></a>Pagamento antecipado do Azure

O pagamento antecipado do Azure não é transferível entre registros. Os saldos do pagamento antecipado do Azure são vinculados contratualmente ao registro em que ele foi solicitado. O pagamento antecipado do Azure não é transferido como parte do processo de transferência do registro ou da conta.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Nenhum serviço afetado para transferências de conta e registro

Não há tempo de inatividade durante uma transferência de conta ou de registro. Ela poderá ser concluída no mesmo dia da solicitação se todas as informações necessárias forem fornecidas.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Transferir uma assinatura do Enterprise para uma assinatura Pagamento Conforme o Uso

Para transferir uma assinatura Enterprise para uma assinatura individual com as tarifas de Pagamento Conforme o Uso, você precisa criar uma solicitação de suporte no Azure Enterprise Portal. Para criar uma solicitação de suporte, selecione **+ Nova solicitação de suporte** na área **Ajuda e Suporte**.

## <a name="change-azure-subscription-or-account-ownership"></a>Alterar a propriedade da conta ou da assinatura do Azure

O Portal do EA do Azure pode transferir assinaturas de um proprietário de conta para outro. Para obter mais informações, confira [Alterar a propriedade de assinatura ou de conta do Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Efeitos da transferência de assinatura

Quando uma assinatura do Azure é transferida para uma conta no mesmo locatário do Azure Active Directory, todos os usuários, os grupos e as entidades de serviço que tinham o [Azure RBAC (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md) para gerenciar recursos mantêm o respectivo acesso.

Para exibir os usuários com acesso RBAC à assinatura:

1. No portal do Azure, abra **Assinaturas**.
2. Selecione a assinatura que deseja exibir e, em seguida, selecione **Controle de Acesso (IAM)** .
3. Selecione **Atribuições de função**. A página de atribuições de função lista todos os usuários que têm acesso RBAC à assinatura.

Se a assinatura for transferida para uma conta em um locatário diferente do Azure AD, todos os usuários, grupos e entidades de serviço que tiverem [RBAC](../../role-based-access-control/overview.md) para gerenciar recursos _perderão_ o acesso. Embora o acesso RBAC não esteja presente, o acesso à assinatura pode estar disponível por meio de mecanismos de segurança, incluindo:

- Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para saber mais, confira [Criar e carregar um certificado de gerenciamento do Azure](../../cloud-services/cloud-services-certs-create.md).
- Chaves de acesso para serviços como Armazenamento. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../../storage/common/storage-account-overview.md).
- Credenciais de Acesso Remoto para serviços como Máquinas Virtuais do Azure.

Se o destinatário precisar restringir o acesso aos respectivos recursos do Azure, ele deverá considerar a possibilidade de atualizar os segredos associados ao serviço. A maioria dos recursos pode ser atualizada usando as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. No menu Hub, selecione **Todos os recursos**.
3. Selecione o recurso.
4. Na página de recursos, selecione **Configurações** para ver e atualizar os segredos existentes.

## <a name="next-steps"></a>Próximas etapas

- Se você precisar de ajuda para solucionar problemas do Portal do EA do Azure, confira [Solucionar problemas de acesso ao Portal do EA do Azure](ea-portal-troubleshoot.md).
---
title: Configurar o Lockbox para Azure Data Box
description: Saiba como usar Sistema de Proteção de Dados do Cliente com Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92124904"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Usar Sistema de Proteção de Dados do Cliente para Azure Data Box (versão prévia)

Azure Data Box é usado para transferir dados do cliente de e para o Azure. Há instâncias em que Suporte da Microsoft pode precisar acessar dados do cliente durante um Solicitação de suporte. Você pode usar Sistema de Proteção de Dados do Cliente como uma interface para revisar e aprovar ou rejeitar essas solicitações de acesso a dados. 

Este artigo aborda como Sistema de Proteção de Dados do Cliente solicitações são iniciadas e rastreadas para Data Box importação, bem como para as ordens de exportação. O artigo se aplica a dispositivos Azure Data Box e Azure Data Box Heavy dispositivos. 

## <a name="devops-workflow-for-data-access"></a>Fluxo de trabalho do DevOps para acesso a dados

A equipe de operações de suporte e Data Box na Microsoft normalmente não acessa os dados do cliente. Eles tentam resolver problemas usando as ferramentas e a telemetria padrão. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Se os problemas não puderem ser resolvidos e exigirem Suporte da Microsoft para investigar ou reparar dados, eles solicitarão acesso elevado por meio do portal JIT (just in time). O portal do JIP valida o nível de permissão, fornece autenticação multifator e também inclui uma aprovação dos aprovadores internos da Microsoft. Por exemplo, o aprovador poderia ser o DevOps Manager. 

Depois que a solicitação de acesso elevado for aprovada por meio do portal JIT, se você tiver habilitado a Lockbox, a Microsoft também exigirá seu consentimento explícito para acessar os dados. O acesso é solicitado e acompanhado por meio do serviço de Sistema de Proteção de Dados do Cliente no Portal. 

Se você não tiver habilitado a Lockbox, seu consentimento não será necessário para acessar os dados.


## <a name="prerequisites-for-access-request"></a>Pré-requisitos para a solicitação de acesso

Antes de começar, verifique se:

1. Você criou uma ordem de Azure Data Box de acordo com as instruções em:
    1. [Tutorial: Azure data box de pedido](data-box-deploy-ordered.md) para ordens de importação.
    1. [Tutorial: order Azure data Box](data-box-deploy-export-ordered.md) para ordens de exportação.

2. Você configurou Sistema de Proteção de Dados do Cliente para Data Box. Esse é um serviço de aceitação. 

    1. O Sistema de Proteção de Dados do Cliente está atualmente em visualização para o serviço Data Box. Para habilitar Sistema de Proteção de Dados do Cliente para Data Box para sua organização, Inscreva-se para [sistema de proteção de dados do cliente para a visualização pública do Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu).
    2. Sistema de Proteção de Dados do Cliente está automaticamente disponível para todos os clientes que têm um plano de suporte do Azure com um nível mínimo de desenvolvedor. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. Uma solicitação de serviço ou um tíquete de suporte já está aberto para esse problema. Para obter informações sobre o tíquete de suporte, consulte [arquivar uma solicitação de serviço para data Box](data-box-disk-contact-microsoft-support.md).


## <a name="track-approve-request-via-lockbox"></a>Rastrear, aprovar solicitação via Lockbox

Para acompanhar e aprovar uma solicitação de acesso aos dados do cliente, siga estas etapas:

1. A Microsoft detecta que há um problema durante o carregamento ou download dos dados no datacenter do Azure. Por exemplo, a ordem de Data Box é interrompida durante o estágio de **cópia de dados** . 

    O engenheiro de suporte se conecta a Data Box por meio da sessão de suporte e tenta solucionar o problema usando ferramentas e telemetria padrão. Se os discos Data Box estiverem bloqueados e os compartilhamentos não estiverem acessíveis, o engenheiro de suporte criará uma solicitação de lockbox. 
 
2. Quando a solicitação é criada, geralmente a notificação vai para o administrador da assinatura, mas você também pode configurar um grupo para notificações. 

3. Você pode ver a solicitação de lockbox no portal do Azure para sua aprovação. 

    ![Solicitação no portal do Azure](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    Para aprovar a solicitação de lockbox do portal, você pode selecionar **aprovar**.

    ![Aprovar solicitação](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    Depois que a solicitação for aprovada, os discos do dispositivo serão desbloqueados e os compartilhamentos poderão ser acessados na sessão de suporte.

4. O engenheiro de suporte resolve o problema de carregamento e desabilita a sessão de suporte.

Depois que o problema for resolvido, o trabalho de cópia de dados irá progredir até a conclusão.


## <a name="next-steps"></a>Próximas etapas

- [Sistema de Proteção de Dados do Cliente para Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->
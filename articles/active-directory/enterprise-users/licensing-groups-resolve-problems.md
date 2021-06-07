---
title: Resolver problemas de atribuição de licença de grupo-Azure Active Directory | Microsoft Docs
description: Como identificar e resolver problemas de atribuição de licença quando você estiver usando o licenciamento baseado em grupo do Azure Active Directory
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceea2dbcf408b6b35ef7c26580278d3979244119
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551531"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de atribuição de licenças para um grupo no Azure Active Directory

O licenciamento baseado em grupo no Azure Active Directory (Azure AD) introduz o conceito de usuários em um estado de erro de licenciamento. Neste artigo, explicaremos os motivos pelos quais os usuários podem acabar tendo esse estado.

Quando você atribui licenças diretamente a usuários individuais, sem usar o licenciamento baseado em grupo, a operação de atribuição pode falhar. Por exemplo, quando você executa o cmdlet do PowerShell `Set-MsolUserLicense` em um sistema de usuário, o cmdlet pode falhar por vários motivos relacionados à lógica de negócios. Por exemplo, pode haver um número insuficiente de licenças ou um conflito entre dois planos de serviço que não podem ser atribuídos ao mesmo tempo. O problema é relatado imediatamente para você.

Quando você está usando o licenciamento com base em grupo, os mesmos erros podem ocorrer, mas eles ocorrem em segundo plano quando o serviço do Azure AD está atribuindo licenças. Por esse motivo, os erros não podem ser comunicados a você imediatamente. Em vez disso, eles são registrados no objeto do usuário e relatados por meio do portal administrativo. Observe que a intenção original de licenciar o usuário nunca é perdida, mas é registrada em um estado de erro para investigação e resolução futuras.

## <a name="find-license-assignment-errors"></a>Localizar erros de atribuição de licença

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Para localizar usuários em um estado de erro em um grupo

1. Abra o grupo em sua página de visão geral e selecione **licenças**. Uma notificação será exibida se houver usuários em um estado de erro.

   ![Mensagem de notificação de erro e grupo](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Selecione a notificação para abrir uma lista de todos os usuários afetados. Você pode selecionar cada usuário individualmente para ver mais detalhes.

   ![lista de usuários no estado de erro de licenciamento de grupo](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Para localizar todos os grupos que contêm pelo menos um erro, na folha **Azure Active Directory**, selecione **Licenças** e, em seguida, **Visão Geral**. Uma caixa de informações é exibida quando grupos exigem sua atenção.

   ![Visão geral e informações sobre grupos em estado de erro](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Selecione a caixa para ver uma lista de todos os grupos com erros. Você pode selecionar cada grupo para obter mais detalhes.

   ![Visão geral e lista de grupos com erros](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

As seções a seguir dão uma descrição de cada possível problema e a maneira de resolvê-lo.

## <a name="not-enough-licenses"></a>Não há licenças suficientes

**Problema:** não há licenças suficientes disponíveis para um dos produtos especificados no grupo. Você precisa adquirir mais licenças para o produto ou liberar as licenças não utilizadas de outros usuários ou grupos.

Para ver quantas licenças estão disponíveis, acesse **Azure Active Directory**  >  **licenças**  >  **todos os produtos**.

Para ver quais usuários e grupos estão consumindo licenças, selecione um produto. Em **Usuários licenciados**, você verá todos os usuários que tiveram licenças atribuídas diretamente ou por meio de um ou mais grupos. Em **Grupos licenciados**, você verá todos os grupos que têm esses produtos atribuídos.

**PowerShell:** os cmdlets do PowerShell reportam esse erro como _CountViolation_.

## <a name="conflicting-service-plans"></a>Planos de serviço conflitante

**Problema:** um dos produtos especificados no grupo contém um plano de serviço que está em conflito com outro plano de serviço já está atribuído ao usuário por meio de um produto diferente. Alguns planos de serviço são configurados de uma maneira que não possam ser atribuídos ao mesmo usuário que outro plano de serviço relacionado.

Considere o exemplo a seguir. Um usuário tem uma licença para o Office 365 Enterprise *E1* atribuída diretamente, com todos os planos habilitados. O usuário foi adicionado a um grupo que tem o Office 365 Enterprise *E3* produto atribuído a ele. O produto E3 contém planos de serviço que não podem se sobrepor aos planos incluídos em E1, portanto, a atribuição de licença de grupo falha com o erro "Planos de serviço conflitantes". Neste exemplo, os planos de serviço conflitantes são:

- O Exchange Online (plano 2) está em conflito com o Exchange Online (plano 1).

Para resolver esse conflito, você precisa desabilitar dois dos planos. Você pode desabilitar a licença de E1 está diretamente atribuída ao usuário. Ou você precisa modificar a atribuição de licença do grupo inteiro e desabilitar os planos na licença E3. Como alternativa, você pode decidir remover a licença de E1 do usuário se ela é redundante no contexto da licença E3.

A decisão de como resolver conflitantes licenças de produtos sempre pertence ao administrador. O Azure AD não resolve conflitos de licença automaticamente.

**PowerShell:** os cmdlets do PowerShell reportam esse erro como _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem desta licença

**Problema:** um dos produtos especificados no grupo contém um plano de serviço deve ser habilitado para outro plano de serviço em outro produto, a função. Esse erro ocorre quando o Azure AD tenta remover o plano de serviço subjacente. Por exemplo, isso pode acontecer quando você remove o usuário do grupo.

Para resolver esse problema, você precisará garantir que o plano necessário ainda esteja atribuído aos usuários por meio de algum outro método ou que os serviços dependentes sejam desabilitados para esses usuários. Depois de fazer isso, você pode remover corretamente a licença de grupo dos usuários.

**PowerShell:** os cmdlets do PowerShell reportam esse erro como _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>O local de uso não é permitido

**Problema:** alguns serviços da Microsoft não estão disponíveis em todos os locais devido a leis e regulamentações locais. Para poder atribuir uma licença a um usuário, você deve especificar a propriedade **Local de uso** para o usuário. Você pode especificar o local na seção de edição de perfil do **usuário**  >    >   no portal do Azure.

Quando o Azure AD tentar atribuir uma licença de grupo a um usuário cujo local de uso não tem suporte, ele falhará e registrará esse erro no usuário.

Para resolver esse problema, remova usuários de locais sem suporte do grupo licenciado. Como alternativa, se os valores atuais de local de uso não representarem o local real do usuário, você poderá modificá-los para que as licenças sejam atribuídas corretamente na próxima vez (se houver suporte para o novo local).

**PowerShell:** os cmdlets do PowerShell reportam esse erro como _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Quando o Azure AD atribui licenças de grupo, quaisquer usuários sem um local de uso especificado herdam o local do diretório. É recomendável que os administradores definam os valores de local de uso corretos nos usuários antes de usar o licenciamento baseado em grupo para cumprir as leis e regulamentações locais.

## <a name="duplicate-proxy-addresses"></a>Endereços de proxy duplicados

Se você usar o Exchange Online, alguns usuários em sua organização poderão estar configurados incorretamente com o mesmo valor de endereço de proxy. Quando o licenciamento baseado em grupo tenta ceder uma licença para esse usuário, ele falha e mostra “O endereço proxy já está sendo usado”.

> [!TIP]
> Para ver se há um endereço de proxy duplicado, execute o seguinte cmdlet do PowerShell no Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Para obter mais informações sobre esse problema, consulte a [mensagem de erro "O endereço Proxy já está sendo usado" no Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). O artigo também inclui informações sobre [como se conectar ao Exchange Online usando o PowerShell remoto](/powershell/exchange/connect-to-exchange-online-powershell).

Depois de solucionar quaisquer problemas de endereço proxy para os usuários afetados, force o processamento de licença no grupo para garantir que as licenças agora possam ser aplicadas.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Alteração de atributo de ProxyAddresses e de email do Azure AD

**Problema:** Ao atualizar a atribuição de licença em um usuário ou grupo, você pode ver que o atributo email e ProxyAddresses do Azure AD de alguns usuários são alterados.

Atualizar a atribuição de licença em um usuário faz com que o cálculo do endereço proxy seja disparado, o que pode alterar os atributos do usuário. Para entender o motivo exato da alteração e resolver o problema, consulte este artigo sobre [como o atributo proxyAddresses é populado no Azure ad](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException em logs de auditoria

**Problema:** O usuário tem LicenseAssignmentAttributeConcurrencyException para a atribuição de licença nos logs de auditoria.
Quando o licenciamento baseado em grupo tenta processar a atribuição de licença simultânea da mesma licença a um usuário, essa exceção é registrada no usuário. Isso geralmente acontece quando um usuário é membro de mais de um grupo com a mesma licença atribuída. O Azure AD tentará processar novamente a licença de usuário e resolverá o problema. Não é necessária nenhuma ação do cliente para corrigir esse problema.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Mais de uma licença de produto atribuída a um grupo

Você pode atribuir mais de uma licença de produto a um grupo. Por exemplo, você pode atribuir o Office 365 Enterprise E3 e o Enterprise Mobility + Security a um grupo para habilitar facilmente todos os serviços incluídos para os usuários.

O Azure AD tenta atribuir todas as licenças que são especificadas no grupo para cada usuário. Se o Azure AD não puder atribuir um dos produtos devido a problemas de lógica de negócios, ele não atribuirá outras licenças no grupo também. Um exemplo é se não houver licenças suficientes para todos ou se houver conflitos com outros serviços que estejam habilitados no usuário.

Você pode ver os usuários que não foram atribuídos e verificar quais produtos são afetados por esse problema.

## <a name="when-a-licensed-group-is-deleted"></a>Quando um grupo licenciado é excluído

Você deve remover todas as licenças atribuídas a um grupo antes de poder excluí-lo. No entanto, remover licenças de todos os usuários no grupo pode levar tempo. Ao remover atribuições de licença de um grupo, pode haver falhas se o usuário tem uma licença dependente atribuída ou se há um problema de conflito de endereço de proxy que proíbe a remoção da licença. Se um usuário tiver uma licença que é dependente de outra licença que está sendo removida devido à exclusão do grupo, a atribuição de licença ao usuário será convertida de herdada para direta.

Por exemplo, considere um grupo que tenha as licenças do Office 365 E3/E5 atribuídas com um plano de serviço Skype for Business habilitado. Imagine também que alguns membros do grupo têm licenças de conferência de áudio atribuídas diretamente. Quando o grupo é excluído, o licenciamento com base em grupo tentará remover as licenças do Office 365 E3/E5 de todos os usuários. Já que a conferência de áudio é dependente do Skype for Business, para todos os usuários com a conferência de áudio atribuída, o licenciamento com base em grupo converte as licenças do Office 365 E3/E5 para a atribuição de licença direta.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Gerenciar licenças para produtos com pré-requisitos

Alguns produtos da Microsoft Online que talvez você tenha são *complementos*. Complementos exigem que um plano de serviço de pré-requisito seja habilitado para um usuário ou um grupo antes que possam receber uma licença. Com o licenciamento baseado em grupo, o sistema exige que tanto os planos de serviço de complemento quanto os de pré-requisito estejam presentes no mesmo grupo. Isso é feito para garantir que qualquer usuário adicionado ao grupo possa receber o produto totalmente funcional. Considere o seguinte exemplo:

O Microsoft Workplace Analytics é um produto complementar. Contém um único plano de serviço com o mesmo nome. Somente podemos atribuir esse plano de serviço um usuário ou grupo, quando um dos seguintes pré-requisitos também é atribuído:

- Exchange Online (Plano 1)
- Exchange Online (Plano 2)

Se tentarmos atribuir este produto por conta própria a um grupo, o portal retornará uma mensagem de notificação. Se selecionarmos os detalhes do item, ele mostrará a seguinte mensagem de erro:

  "Falha na operação de licença. Certifique-se de que o grupo possua os serviços necessários, antes de adicionar ou remover um serviço dependente. **O serviço Microsoft Workplace Analytics requer o Exchange Online (plano 2) para ser habilitado também.**"

Para atribuir essa licença complementar a um grupo, é necessário garantir que o grupo também contenha o plano de serviço de pré-requisito. Por exemplo, é possível atualizar um grupo existente que já contenha o produto Office 365 E3 completo e adicionar o produto complementar a ele.

Também é possível criar um grupo independente que contenha apenas os produtos mínimos necessários para fazer o complemento funcionar. Ele pode ser usado para licenciar apenas os usuários selecionados para o produto de complemento. Com base no exemplo anterior, você atribuiria os seguintes produtos ao mesmo grupo:

- Office 365 Enterprise E3 com apenas o plano de serviço do Exchange Online (Plano 2) habilitado
- Microsoft Workplace Analytics

De agora em diante, qualquer usuário adicionado a esse grupo consumirá uma licença do produto E3 e uma licença do produto Workplace Analytics. Ao mesmo tempo, esses usuários podem ser membros de outro grupo que lhes dê o produto E3 completo e eles ainda consumirão apenas uma licença para esse produto.

> [!TIP]
> É possível criar vários grupos para cada plano de serviço de pré-requisito. Por exemplo, se você utilizar tanto o Office 365 Enterprise E1 quanto o Office 365 Enterprise E3 para seus usuários, você poderá criar dois grupos para licença do Microsoft Workplace Analytics: um que use E1 como pré-requisito e o outro que use E3. Isso permite que você distribua o complemento aos usuários E1 e E3 sem consumir licenças adicionais.

## <a name="force-group-license-processing-to-resolve-errors"></a>Forçar o processamento de licença de grupo para resolver erros

Dependendo de quais etapas você executou para resolver os erros, talvez seja necessário disparar manualmente o processamento de um grupo para atualizar o estado do usuário.

Por exemplo, se você tiver liberado algumas licenças removendo atribuições de licença diretas dos usuários, precisará disparar o processamento de grupos que falharam anteriormente para licenciar totalmente todos os membros usuários. Para reprocessar um grupo, vá para o painel de grupo, abra **Licenças** e, em seguida, selecione o botão **Reprocessar** na barra de ferramentas.

## <a name="force-user-license-processing-to-resolve-errors"></a>Forçar o processamento de licença de usuário para resolver erros

Dependendo de quais etapas você executou para resolver os erros, talvez seja necessário disparar manualmente o processamento de um usuário para atualizar o estado dos usuários.

Por exemplo, depois de resolver o problema de endereço proxy duplicado para um usuário afetado, você precisará disparar o processamento do usuário. Para reprocessar um usuário, vá para o painel de usuário, abra **Licenças** e, em seguida, selecione o botão **Reprocessar** na barra de ferramentas.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de grupos, leia o seguinte:

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuição de licenças a um grupo no Azure Active Directory](./licensing-groups-assign.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar usuários entre licenças de produto usando o licenciamento baseado em grupo no Microsoft Azure Active Directory](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](./licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento baseado em grupo no Azure Active Directory](licensing-ps-examples.md)
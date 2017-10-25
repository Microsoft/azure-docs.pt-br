---
title: "Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory | Microsoft Docs"
description: "Mais cenários de licenciamento baseado em grupo do Azure Active Directory"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: piotrci
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 75cafa6868d54f9d8a7e0dbe9f2a9e85ed43f16f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Cenários, limitações e problemas conhecidos com o uso de grupos para gerenciar o licenciamento no Azure Active Directory

Use as informações e exemplos a seguir para obter uma compreensão mais avançada do licenciamento baseado em grupo do Azure AD (Azure Active Directory).

## <a name="usage-location"></a>Local de uso

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade **Local de uso** para o usuário. No [portal do Azure](https://portal.azure.com), especifique em **Usuário** &gt; **Perfil** &gt; **Configurações**.

Para a atribuição de licenças de grupo, qualquer usuário sem um local de uso especificado herdará o local do diretório. Se você tiver usuários em localizações diferentes, reflita isso corretamente nos objetos de usuário antes de adicionar usuários a grupos com licenças.

> [!NOTE]
> A atribuição de licença de grupo nunca modificará um valor existente de localização de uso em um usuário. Recomendamos sempre definir a localização de uso como parte do fluxo de criação do usuário no Azure AD (por exemplo, por meio da configuração do AAD Connect) – isso garantirá que o resultado da atribuição de licença sempre seja correto e que os usuários não recebam serviços em localizações que não são permitidas.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Usar o licenciamento baseado em grupo com grupos dinâmicos

Você pode usar o licenciamento baseado em grupo com qualquer grupo de segurança, o que significa que ele pode ser combinado a grupos dinâmicos do Azure AD. Os grupos dinâmicos executam regras em relação a atributos de objeto de usuário para adicionar e remover usuários automaticamente dos grupos.

Por exemplo, você pode criar um grupo dinâmico para um conjunto de produtos que você deseja atribuir aos usuários. Cada grupo é populado por uma regra com a adição de usuários por seus atributos e a cada grupo são atribuídas as licenças que você deseja que ele receba. Você pode atribuir o atributo local e sincronizá-lo com o Azure AD ou pode gerenciar o atributo diretamente na nuvem.

As licenças são atribuídas ao usuário logo depois que ele é adicionado ao grupo. Quando o atributo é alterado, o usuário deixa os grupos e as licenças são removidas.

### <a name="example"></a>Exemplo

Considere um exemplo de solução de gerenciamento de identidade local que decide quais usuários devem ter acesso aos serviços Web da Microsoft. Usa **extensionAttribute1** para armazenar um valor de cadeia de caracteres que representa as licenças que o usuário deve ter. O Azure AD Connect é sincronizado com o Azure AD.

Os usuários podem precisar de uma licença, mas não da outra ou talvez precisem de ambas. Este é um exemplo no qual você está distribuindo licenças do Office 365 Enterprise E5 e do EMS (Enterprise Mobility + Security) para os usuários em grupos:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: serviços de base

![Captura de tela de serviços de base do Office 365 Enterprise E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: usuários licenciados

![Captura de tela de usuários licenciados do Enterprise Mobility + Security](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

Neste exemplo, modifique um usuário e defina seu extensionAttribute1 com o valor `EMS;E5_baseservices;`, se desejar que o usuário tenha ambas as licenças. Você pode fazer essa modificação local. Depois que a alteração é sincronizada com a nuvem, o usuário é adicionado automaticamente a ambos os grupos, e as licenças são atribuídas.

![Captura de tela mostrando como definir extensionAttribute1 do usuário](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Tenha cuidado ao modificar uma regra de associação de um grupo existente. Quando uma regra for alterada, a associação do grupo será reavaliada e os usuários que não correspondem mais à nova regra serão removidos (os usuários que ainda correspondem à nova regra não serão afetados durante esse processo). Esses usuários terão suas licenças removidas durante o processo, o que poderá resultar na perda de serviços ou, em alguns casos, na perda de dados.

> Se você tiver um grupo dinâmico grande do qual depende para a atribuição de licença, considere a possibilidade de validar as alterações principais em um grupo de teste menor antes de aplicá-las ao grupo principal.

## <a name="multiple-groups-and-multiple-licenses"></a>Vários grupos e várias licenças

Um usuário pode ser membro de vários grupos com licenças. Estas são algumas coisas que você deve considerar:

- Várias licenças para o mesmo produto podem se sobrepor e resultam na aplicação de todos os serviços habilitados ao usuário. O exemplo a seguir mostra dois grupos de licenciamento: *serviços de base E3* contém os serviços de base para implantar em primeiro lugar, para todos os usuários. E *serviços estendidos E3* contém serviços adicionais (Sway e Planejador) para implantação apenas para alguns usuários. Neste exemplo, o usuário foi adicionado aos dois grupos:

  ![Captura de tela de serviços habilitados](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Como resultado, o usuário tem sete dos 12 serviços no produto habilitados, enquanto usa apenas uma licença para esse produto.

- A seleção da licença *E3* mostra mais detalhes, incluindo informações sobre quais grupos causaram a habilitação de quais serviços para o usuário.

  ![Captura de tela de serviços habilitados por grupo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>As licenças diretas coexistem com as licenças de grupo

Quando um usuário herda uma licença de um grupo, não é possível remover nem modificar essa atribuição de licença nas propriedades do usuário diretamente. As alterações devem ser feitas no grupo e, depois, propagadas para todos os usuários.

No entanto, é possível, atribuir a mesma licença do produto diretamente ao usuário, além da licença herdada. Habilite serviços adicionais no produto apenas para um usuário, sem afetar outros usuários.

Licenças atribuídas diretamente podem ser removidas e não afetam as licenças herdadas. Considere o usuário que herda uma licença do Office 365 Enterprise E3 de um grupo.

1. Inicialmente, o usuário herda a licença apenas do grupo *Serviços básicos E3*, o que habilita quatro planos de serviço, conforme mostrado:

  ![Captura de tela de serviços habilitados para grupo E3](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Selecione **Atribuir** para atribuir uma licença E3 diretamente ao usuário. Nesse caso, você desabilitará todos os planos de serviço, exceto o Yammer Enterprise:

  ![Captura de tela de como atribuir uma licença diretamente a um usuário](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Como resultado, o usuário ainda usa apenas uma licença do produto E3. Porém, a atribuição direta habilita o serviço do Yammer Enterprise somente para esse usuário. Veja quais serviços são habilitados pela associação a um grupo em comparação com a atribuição direta:

  ![Captura de tela de atribuição herdada versus direta](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Quando você usa a atribuição direta, são permitidas as seguintes operações:

  - O Yammer Enterprise pode ser desativado diretamente no objeto do usuário. A alternância **Ativar/Desativar** na ilustração está habilitada nesse serviço, ao contrário das outras alternâncias de serviço. Como o serviço está habilitado diretamente no usuário, ele pode ser modificado.
  - Outros serviços também podem ser habilitados como parte da licença atribuída diretamente.
  - O botão **Remover** pode ser usado para remover a licença direta do usuário. Veja que agora o usuário tem apenas a licença de grupo herdada e somente os serviços originais permanecem habilitados:

    ![Captura de tela mostrando como remover a atribuição direta](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Gerenciando novos serviços adicionados a produtos
Quando a Microsoft adicionar um novo serviço a um produto, ele será habilitado por padrão em todos os grupos aos quais você atribuiu a licença do produto. Os usuários no locatário que estão inscritos em notificações sobre alterações do produto receberão emails antecipadamente, notificando sobre as próximas adições de serviço.

Como administrador, você pode examinar todos os grupos afetados pela alteração e tomar uma ação, como desabilitar o novo serviço em cada grupo. Por exemplo, se você criou grupos que direcionam apenas serviços específicos para implantação, reveja esses grupos e verifique se os serviços recém-adicionados estão desabilitados.

Este é um exemplo da aparência desse processo:

1. Originalmente, você atribuiu o produto *Office 365 Enterprise E5* a vários grupos. Um desses grupos, chamado *O365 E5 – somente Exchange* foi criado para habilitar apenas o serviço *Exchange Online (Plano 2)* para seus membros.

2. Você recebeu uma notificação da Microsoft informando que o produto E5 será estendido com um novo serviço – o *Microsoft Stream*. Quando o serviço estiver disponível no locatário, você poderá fazer o seguinte:

3. Acesse a folha [**Azure Active Directory > Licenças > Todos os produtos**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products), selecione *Office 365 Enterprise E5* e, em seguida, selecione **Grupos Licenciados** para exibir uma lista de todos os grupos com o produto.

4. Clique no grupo que você deseja examinar (nesse caso, *O365 E5 – somente Exchange*). Isso abrirá a guia **Licenças**. Se você clicar na licença do E5, uma folha listando todos os serviços habilitados será aberta.
> [!NOTE]
> O serviço *Microsoft Stream* foi automaticamente adicionado e habilitado nesse grupo, além do serviço *Exchange Online*:

  ![Captura de tela do novo serviço adicionado a uma licença de grupo](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. Se você desejar desabilitar o novo serviço nesse grupo, clique na alternância **Ativar/Desativar** ao lado do serviço e clique no botão **Salvar** para confirmar a alteração. O Azure AD agora processará todos os usuários no grupo para aplicar a alteração; todos os novos usuários adicionados ao grupo não terão o serviço *Microsoft Stream* habilitado.

  > [!NOTE]
  > Os usuários ainda podem ter o serviço habilitado por meio de alguma outra atribuição de licença (outro grupo do qual são membros ou uma atribuição de licença direta).

6. Se necessário, realize as mesmas etapas para outros grupos com esse produto atribuído.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Usar o PowerShell para ver quem tem licenças herdadas e diretas
Use um script do PowerShell para verificar se os usuários têm uma licença atribuída diretamente ou herdada de um grupo.

1. Execute o cmdlet `connect-msolservice` para autenticar e conectar-se ao locatário.

2. `Get-MsolAccountSku` pode ser usado para descobrir todas as licenças de produto provisionadas no locatário.

  ![Captura de tela do cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Use o valor *AccountSkuId* para a licença de seu interesse com [este script do PowerShell](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Isso produzirá uma lista de usuários que têm essa licença, com as informações sobre como a licença é atribuída.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Usar os Logs de auditoria para monitorar as atividades de licenciamento baseado em grupo

Use os [logs de auditoria do Azure AD](./active-directory-reporting-activity-audit-logs.md#audit-logs) para ver todas as atividades relacionadas ao licenciamento baseado em grupo, incluindo:
- quem alterou licenças em grupos
- quando o sistema iniciou o processamento de uma alteração de licença de grupo e quando isso foi concluído
- quais alterações de licença foram feitas em um usuário como resultado de uma atribuição de licença de grupo.

>[!NOTE]
> Os logs de auditoria estão disponíveis na maioria das folhas na seção Azure Active Directory do portal. Dependendo de onde você acessá-los, os filtros podem ser pré-aplicados para mostrar apenas as atividades relevantes para o contexto da folha. Se você não estiver vendo os resultados esperados, examine [as opções de filtragem](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs) ou acesse os logs de auditoria não filtrados em [**Azure Active Directory > Atividade > Logs de auditoria**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Descobrir quem modificou uma licença de grupo

1. Defina o filtro **Atividade** como *Definir licença de grupo* e clique em **Aplicar**.
2. Os resultados incluem todos os casos de licenças que estão sendo definidas ou modificadas em grupos.
>[!TIP]
> Você também pode digitar o nome do grupo no filtro *Destino* para definir o escopo dos resultados.

3. Clique em um item da exibição de lista para ver os detalhes do que foi alterado. Em *Propriedades Modificadas*, valores antigos e novos para a atribuição de licença são listados.

Este é um exemplo de alterações recentes de licença de grupo, com detalhes:

![Captura de tela de alterações de licença de grupo](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Descobrir quando as alterações de grupo iniciaram e concluíram o processamento

Quando uma licença for alterada em um grupo, o Azure AD começará a aplicar as alterações a todos os usuários.

1. Para ver quando os grupos iniciaram o processamento, defina o filtro **Atividade** como *Começar a aplicar a licença baseada em grupo aos usuários*. Observe que o ator da operação é o *Licenciamento Baseado em Grupo do Microsoft Azure AD* – uma conta do sistema usada para executar todas as alterações de licença de grupo.
>[!TIP]
> Clique em um item da lista para ver o campo *Propriedades Modificadas* – ele mostra as alterações de licença selecionadas para processamento. Isso é útil se você fez várias alterações em um grupo e não tem certeza de qual delas foi processada.

2. Da mesma forma, para ver quando os grupos concluíram o processamento, use o valor de filtro *Concluir a aplicação de licença baseada em grupo aos usuários*.
>[!TIP]
> Nesse caso, o campo *Propriedades Modificadas* contém um resumo dos resultados – isso é útil para verificar rapidamente se o processamento resultou em erros. Resultado de exemplo:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. Para ver o log completo de como um grupo foi processado, incluindo todas as alterações do usuário, defina os seguintes filtros:
  - **Iniciado Por (Ator)**: “Licenciamento Baseado em Grupo do Microsoft Azure AD”
  - **Intervalo de Datas** (opcional): intervalo personalizado para quando você sabe que um grupo específico iniciou e concluiu o processamento

Essa saída de exemplo mostra o início do processamento, todas as alterações do usuário resultantes e a conclusão do processamento.

![Captura de tela de alterações de licença de grupo](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Se você clicar nos itens relacionados a *Alterar licença de usuário*, os detalhes das alterações de licença aplicadas a cada usuário individual serão mostrados.

## <a name="deleting-a-group-with-an-assigned-license"></a>Excluir um grupo com uma licença atribuída

Não é possível excluir um grupo com uma licença ativa atribuída. Um administrador pode excluir um grupo sem perceber que fará com que a licenças sejam removidas dos usuários; por esse motivo, precisamos que as licenças sejam removidas do grupo primeiro, antes que ele possa ser excluído.

Ao tentar excluir um grupo no portal do Azure, pode ser que você veja uma notificação de erro como esta: ![Falha na exclusão do grupo de captura de tela](media/active-directory-licensing-group-advanced/groupdeletionfailed.png)

Vá para a guia **Licenças** no grupo e verifique se há alguma licença atribuída. Em caso afirmativo, remova essas licenças e tente excluir o grupo novamente.

Você pode ver erros semelhantes ao tentar excluir o grupo por meio do PowerShell ou API do Graph. Se você estiver usando um grupo sincronizado do local, o Azure AD Connect também pode relatar erros se houver falha ao excluir o grupo no Azure AD. Em todos esses casos, lembre-se de verificar se há licenças atribuídas ao grupo e remova-as primeiro.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

Se você usa o licenciamento baseado em grupo, convém se familiarizar com a lista de limitações e problemas conhecidos a seguir.

- No momento, o licenciamento baseado em grupo não dá suporte a grupos que contêm outros grupos (grupos aninhados). Se você aplicar uma licença a um grupo aninhado, somente os membros imediatos de primeiro nível do grupo terão as licenças aplicadas.

- O recurso pode ser usado apenas com grupos de segurança. Atualmente, não há suporte para os grupos do Office e você não poderá usá-los no processo de atribuição de licença.

- No momento, o [Portal de Administração do Office 365](https://portal.office.com ) não oferece suporte ao licenciamento baseado em grupo. Se um usuário herdar uma licença de um grupo, essa licença aparecerá no portal de administração do Office como uma licença de usuário comum. Se você tentar modificar essa licença ou tentar remover a licença, o portal retornará uma mensagem de erro. Licenças herdadas de grupos não podem ser modificadas diretamente em um usuário.

- Quando um usuário é removido de um grupo e perde a licença, os planos de serviço dessa licença (por exemplo, SharePoint Online) são definidos como um estado **Suspenso**. Os planos de serviço não são definidos com um estado final desabilitado. Essa precaução pode evitar a remoção acidental de dados do usuário, caso um administrador cometa um erro no gerenciamento de associação a um grupo.

- Quando as licenças são atribuídas ou modificadas para um grupo grande (por exemplo, 100.000 usuários), isso pode afetar o desempenho. Especificamente, o volume de alterações geradas pela automação do Azure AD pode afetar negativamente o desempenho da sincronização de diretório entre o Azure AD e sistemas locais.

- Em determinadas situações de carga alta, o processamento de licença pode ser atrasado e alterações, como adição/remoção de um grupo de licenças ou adicionar/remover usuários do grupo, podem levar muito tempo para serem processadas. Se você observar que suas alterações estão demorando mais do que 24 horas para serem processadas, por favor, [abra um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) para que possamos investigar. Nós melhoraremos as características de desempenho desse recurso antes de atingir a *Disponibilidade geral*.

- A automação de gerenciamento de licença não reage automaticamente a todos os tipos de alteração no ambiente. Por exemplo, você pode ficar sem licenças, colocando alguns usuários em um estado de erro. Para liberar a contagem de estações disponíveis, você pode remover algumas licenças atribuídas diretamente de outros usuários. No entanto, o sistema não reage automaticamente a essa alteração e corrige os usuários nesse estado de erro.

  Como solução alternativa para esses tipos de limitações, acesse a folha **Grupo** no Azure AD e clique em **Reprocessar**. Esse comando processa todos os usuários nesse grupo e resolve os estados de erro, se possível.

- O licenciamento baseado em grupo não registra erros quando uma licença não pôde ser atribuída a um usuário devido a uma configuração de endereço proxy duplicado no Exchange Online; esses usuários são ignorados durante a atribuição de licença. Para obter mais informações sobre como identificar e resolver esse problema, consulte [esta seção](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de licenciamento baseado em grupo, confira:

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

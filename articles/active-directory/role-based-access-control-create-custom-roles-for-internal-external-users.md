---
title: "Criar funções personalizadas de Controle de Acesso Baseado em Função e atribuir a usuários internos e externos no Azure | Microsoft Docs"
description: "Atribuir funções RBAC personalizadas criadas usando o PowerShell e a CLI para usuários internos e externos"
services: active-directory
documentationcenter: 
author: andreicradu
manager: catadinu
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/10/2017
ms.author: a-crradu
ms.translationtype: HT
ms.sourcegitcommit: 890acae2aebf7684e567b9b49377ca7b6da95245
ms.openlocfilehash: cccd0af0c991efe330567c2459717798d116e68f
ms.contentlocale: pt-br
ms.lasthandoff: 09/20/2017

---
## <a name="intro-on-role-based-access-control"></a>Introdução ao controle de acesso baseado em função do Azure

Controle de acesso baseado em função é um recurso apenas do portal do Azure que permite aos proprietários de uma assinatura atribuir funções granulares a outros usuários, que podem gerenciar escopos de recurso específicos no próprio ambiente.

RBAC permite um melhor gerenciamento de segurança para grandes empresas e SMBs que trabalham com colaboradores externos, fornecedores ou freelancers que precisam de acesso a recursos específicos em seu ambiente, mas não necessariamente a toda a infraestrutura nem nenhum escopo relacionado à cobrança. O RBAC proporciona a flexibilidade de ter uma assinatura do Azure gerenciada pela conta de administrador (função de administrador de serviços no nível da assinatura) e ter vários usuários convidados para trabalhar na mesma assinatura, mas sem os direitos administrativos para ela. De uma perspectiva de cobrança e gerenciamento, o recurso RBAC é uma opção eficiente em termos de tempo e gerenciamento para o uso do Azure em vários cenários.

## <a name="prerequisites"></a>Pré-requisitos
Usar o RBAC no ambiente do Azure requer:

* Ter uma assinatura autônoma do Azure atribuída ao usuário como o proprietário (função de assinatura)
* Ter a função de Proprietário da assinatura do Azure
* Ter acesso ao [portal do Azure](https://portal.azure.com)
* Os seguintes Provedores de Recursos devem estar registrado para a assinatura de usuário: **Microsoft.Authorization**. Para obter mais informações sobre como registrar os provedores de recursos, consulte [Provedores, regiões, versões de API e esquemas do Resource Manager](/azure-resource-manager/resource-manager-supported-services.md).
<!---Loc Comment: Link [Resource Manager providers, regions, API versions and schemas] is broken with an error message "404 - Content Not Found---->

> [!NOTE]
> As assinaturas do Office 365 ou licenças do Active Directory do Azure (por exemplo: Acesso ao Azure Active Directory) provisionadas no portal do O365 não se qualificam para uso do RBAC.

## <a name="how-can-rbac-be-used"></a>Como o RBAC pode ser usado
O RBAC pode ser aplicado em três escopos diferentes no Azure. São eles, do escopo mais alto ao mais baixo:

* Assinatura (mais alto)
* Grupo de recursos
* Escopo do recurso (o menor nível de acesso oferecendo permissões direcionadas a um escopo de recursos do Azure individual)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Atribuir funções de RBAC no escopo de assinatura
Há dois exemplos comuns de momentos em que o RBAC é usado (entre outros):

* Quando há usuários externos às organizações (que não fazem parte do locatário do Azure Active Directory do usuário administrador) convidados para gerenciar determinados recursos ou a assinatura inteira
* Ao trabalhar com usuários de dentro a organização (que fazem parte do locatário do Azure Active Directory do usuário), mas que fazem parte de equipes ou grupos diferentes que precisam de acesso granular a toda a assinatura ou a determinados grupos de recursos ou escopos de recursos no ambiente

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Conceder acesso no nível da assinatura para um usuário fora do Azure Active Directory
Funções de RBAC podem ser concedidas somente por **Proprietários** da assinatura, portanto, o usuário administrador deve estar conectado com um nome de usuário que tenha essa função previamente atribuída ou tenha criado a assinatura do Azure.

No portal do Azure, depois de entrar como administrador, selecione "Assinaturas" e escolha a desejada.
![folha de assinatura no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) Por padrão, se o usuário administrador tiver comprado a assinatura do Azure, ele aparecerá como **Administrador da conta**, sendo essa a função da assinatura. Para obter mais detalhes sobre as funções de assinatura do Azure, consulte [Adicionar ou alterar funções de administrador do Azure que gerenciam a assinatura ou os serviços](/billing/billing-add-change-azure-subscription-administrator.md).

Neste exemplo, o usuário "alflanigan@outlook.com" é o **Proprietário** da assinatura de "Avaliação Gratuita" no locatário AAD definido como "Locatário padrão do Azure". Uma vez que esse usuário é o criador da assinatura do Azure com a Conta da Microsoft inicial "Outlook" (Conta da Microsoft = Outlook, Live etc.), o nome de domínio padrão para todos os outros usuários adicionados neste locatário será **"@alflaniganuoutlook.onmicrosoft.com"**. Por design, a sintaxe do novo domínio é formada reunindo o nome de usuário e o nome de domínio do usuário que criou o locatário e adicionando a extensão **".onmicrosoft.com"**.
Além disso, os usuários podem entrar com um nome de domínio personalizado no locatário depois de o adicionarem e verificarem para o novo locatário. Para obter mais detalhes sobre como verificar um nome de domínio personalizado em um locatário do Azure Active Directory, consulte [Adicionar um nome de domínio personalizado ao seu diretório](/active-directory/active-directory-add-domain).

Neste exemplo, o diretório "Locatário padrão do Azure" contém somente usuários com o nome de domínio "@alflanigan.onmicrosoft.com".

Depois de selecionar a assinatura, o usuário administrador deve clicar em **Controle de acesso (IAM)** e, em seguida, em **Adicionar uma nova função**.





![recurso IAM de controle de acesso no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![adicionar novo usuário no recurso IAM de controle de acesso no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

A próxima etapa é selecionar a função a ser atribuída e o usuário ao qual a função de RBAC será atribuída. No menu suspenso **Função**, o usuário administrador vê apenas as funções RBAC internas que estão disponíveis no Azure. Para obter explicações mais detalhadas sobre cada função e seus escopos atribuíveis, consulte [Funções internas para o Controle de Acesso Baseado em Função do Azure](/active-directory/role-based-access-built-in-roles.md).
<!---Loc Comment: Link [Built-in roles for Azure Role-Based Access Control] is broken with an error message "404 - Content Not Found---->

Em seguida, o usuário administrador precisa adicionar o endereço de email do usuário externo. O comportamento esperado para o usuário externo é não aparecer no locatário existente. Depois de ser convidado, o usuário externo fica visível em **Assinaturas > Controle de Acesso (IAM)** com todos os usuários atuais que são atribuídos a uma função RBAC no escopo de Assinatura no momento.





![adicionar permissões à nova função RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![lista de funções de RBAC no nível de assinatura](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

O usuário "chessercarlton@gmail.com" foi convidado a ser um **Proprietário** para a assinatura de "Avaliação Gratuita". Depois de enviar o convite, o usuário externo receberá um email de confirmação com um link de ativação.
![convite por email para a função RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Sendo externo à organização, o novo usuário não tem nenhum atributo existente no diretório "Locatário padrão do Azure". Ele será criado depois que o usuário externo tiver autorizado a ser gravado no diretório associado à assinatura para a qual ele recebeu uma função.





![mensagem de convite de email para a função de RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

O usuário externo aparece no locatário do Azure Active Directory de agora em diante como usuário externo e isso pode ser exibido tanto no portal do Azure quanto no portal clássico.





![folha dos usuários no azure active-directory, portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)





![folha dos usuários no azure active-directory, portal clássico do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/8.png)

Na exibição **Usuários** nos dois portais, os usuários externos podem ser reconhecidos pelo:

* Tipo de ícone diferente no portal do Azure
* Ponto de origem diferente no portal clássico

No entanto, conceder acesso de **Proprietário** ou **Colaborador** a um usuário externo no escopo da **Assinatura** não permite acesso ao diretório do usuário administrador, a menos que o **Administrador Global** o permita. Nas propriedades do usuário, o **Tipo de Usuário** que tem dois parâmetros comuns, **Membro** e **Convidado** pode ser identificado. Um membro é um usuário registrado no diretório, enquanto um convidado é um usuário convidado para o diretório de uma fonte externa. Para obter mais informações, consulte [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B](/active-directory/active-directory-b2b-admin-add-users).
<!---Loc Comment: Link [How do Azure Active Directory admins add B2B collaboration users] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> Certifique-se de que, depois de inserir as credenciais no portal, o usuário externo selecione o diretório correto para entrar. O mesmo usuário pode ter acesso a vários diretórios e pode selecionar qualquer um deles clicando no nome de usuário na parte superior direita no portal do Azure e escolhendo o diretório apropriado na lista suspensa.

Enquanto for um convidado no diretório, o usuário externo poderá gerenciar todos os recursos da assinatura do Azure, mas não poderá acessar o diretório.





![acesso restrito ao azure active-directory, portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

O Azure Active Directory e uma assinatura do Azure não têm uma relação de pai-filho como outros recursos do Azure (por exemplo: máquinas virtuais, redes virtuais, aplicativos Web, armazenamento etc.) têm com uma assinatura do Azure. Todos estes segundos são criados, gerenciados e cobrados em uma assinatura do Azure, enquanto uma assinatura do Azure é usada para gerenciar o acesso a um diretório do Azure. Para saber mais, consulte [Como uma assinatura do Azure está relacionada ao Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

De todas as funções RBAC internas, **Proprietário** e **Colaborador** oferecem acesso de gerenciamento total a todos os recursos no ambiente, sendo a diferença que um Colaborador não pode criar nem excluir novas funções de RBAC. Outras funções internas, como **Colaborador de Máquina Virtual** oferecem acesso de gerenciamento completo apenas aos recursos indicados pelo nome, não importa o **Grupo de Recursos** em que eles estão sendo criados.

Atribuir a função de RBAC interna de **Colaborador de Máquina Virtual** em um nível de assinatura significa que o usuário atribuído à função:

* Pode exibir todas as máquinas virtuais, não importa a data de implantação nem os grupos de recursos dos quais fazem parte
* Tem acesso de gerenciamento completo para as máquinas virtuais na assinatura
* Não é possível exibir outros tipos de recursos na assinatura
* Não é possível operar nenhuma alteração de uma perspectiva de cobrança

> [!NOTE]
> Sendo RBAC um recurso único portal do Azure, ele não concede acesso ao portal clássico.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Atribuir uma função de RBAC interna a um usuário externo
Para um cenário diferente nesse teste, o usuário externo "alflanigan@gmail.com" é adicionado como um **Colaborador de Máquina Virtual**.




![função interna de colaborador de máquina virtual](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

O comportamento normal para esse usuário externo com essa função interna é ver e gerenciar somente máquinas virtuais e seus recursos do Resource Manager adjacentes necessários ao implantar. Por design, essas funções limitadas oferecem acesso somente aos recursos correspondentes criado no portal do Azure, independentemente de alguns ainda poderem ser implantados no portal clássico também (por exemplo: máquinas virtuais).





![visão geral da função de colaborador de máquina virtual no portal do azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Conceder acesso no nível da assinatura para um usuário no mesmo diretório
O fluxo do processo é idêntico a adicionar um usuário externo, tanto da perspectiva do administrador que está concedendo a função RBAC quanto do usuário que está recebendo acesso à função. A diferença aqui é que o usuário convidado não receberá nenhum convite por email, pois todos os escopos de recursos dentro da assinatura estarão disponíveis no painel depois do logon.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Atribuir funções de RBAC no escopo do grupo de recursos
Atribuir uma função de RBAC em um escopo de **Grupo de Recursos** tem um processo idêntico ao de atribuir a função no nível de assinatura para os dois tipos de usuários, externos e internos (parte do mesmo diretório). Os usuários que recebem a função de RBAC devem ver em seu ambiente somente o grupo de recursos para o qual eles receberam acesso usando o ícone **Grupos de Recursos** no portal do Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Atribuir funções de RBAC no escopo do recurso
Atribuir uma função de RBAC no escopo de um recurso no Azure tem um processo idêntico ao de atribuir uma função no nível de assinatura ou no nível do grupo de recursos, seguindo o mesmo fluxo de trabalho em ambos os cenários. Novamente, os usuários que recebem a função de RBAC podem ver apenas os itens aos quais eles receberam acesso, seja na guia **Todos os Recursos** ou diretamente no painel.

Um aspecto importante para RBAC tanto no escopo do grupo de recursos quanto no escopo do recurso é os usuários entrarem no diretório correto.





![logon do diretório no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Atribuir funções de RBAC a um grupo do Azure Active Directory
Todos os cenários que usam RBAC nos três escopos diferentes no Azure oferecem o privilégio de gerenciar, implantar e administrar vários recursos como um usuário atribuído sem a necessidade de gerenciar uma assinatura pessoal. Independentemente da função de RBAC atribuída a uma assinatura, grupo de recursos ou escopo de recurso, todos os recursos criados mais adiante pelos usuários atribuídos são cobrados sob a única assinatura do Azure à qual os usuários têm acesso. Dessa forma, os usuários que têm permissões de administrador de cobrança para toda essa assinatura do Azure têm uma visão geral completa do consumo, não importa quem está gerenciando os recursos.

Para organizações maiores, as funções de RBAC podem ser aplicadas da mesma maneira para grupos do Azure Active Directory considerando a perspectiva de que o usuário administrador deseja conceder acesso granular a equipes ou departamentos inteiros, e não individualmente a cada usuário, considerando, portanto, essa opção muito eficiente em termos de tempo e gerenciamento. Para ilustrar esse exemplo, a função de **Colaborador** foi adicionada a um dos grupos no locatário no nível da assinatura.





![adicionar função de RBAC a grupos do AAD](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Esses grupos são grupos de segurança provisionados e gerenciados somente dentro do Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Crie uma função de RBAC personalizada para abrir as solicitações de suporte usando o PowerShell
As funções de RBAC internas que estão disponíveis no Azure garantem determinados níveis de permissão conforme os recursos disponíveis no ambiente. No entanto, caso nenhuma dessas funções atenda às necessidades do usuário administrador, existe a opção de limitar o acesso ainda mais criando funções de RBAC personalizadas.

Criar funções de RBAC personalizadas requer colocar uma função interna, editá-la e, em seguida, importá-la novamente no ambiente. O download e o upload da função são gerenciados usando o PowerShell ou a CLI.

É importante entender os pré-requisitos da criação de uma função personalizada que podem conceder acesso granular no nível da assinatura e também dar ao usuário convidado a flexibilidade de abrir solicitações de suporte.

Neste exemplo, a função interna **Leitor** que dá aos usuários acesso para exibir todos os escopos de recursos, mas não para editá-los nem criar novos, foi personalizada para dar ao usuário a opção de abrir solicitações de suporte.

A primeira ação de exportar a função **Leitor** precisa ser concluída no PowerShell executada com permissões elevadas, como administrador.

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![Captura de tela do PowerShell para a função do RBAC de Leitor](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

Em seguida, você precisa extrair o modelo JSON da função.





![Modelo JSON para a função do RBAC de Leitor personalizada](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

Uma função RBAC típica é composta por três seções principais, **Actions**, **NotActions** e **AssignableScopes**.

Na seção **Action**, são listadas todas as operações permitidas para essa função. É importante entender que cada ação é atribuída de um provedor de recursos. Nesse caso, para criar tíquetes de suporte, o provedor de recursos **Microsoft.Support** deve ser listado.

Para ser capaz de ver todos os provedores de recursos disponíveis e registrados em sua assinatura, você pode usar o PowerShell.
```
Get-AzureRMResourceProvider

```
Além disso, você pode verificar os todos os cmdlets do PowerShell disponíveis para gerenciar os provedores de recursos.
    ![Captura de tela do PowerShell para gerenciamento do provedor de recursos](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

Para restringir todas as ações para uma determinada função RBAC, os provedores de recursos são listados na seção **NotActions**.
Por fim, é obrigatório que a função RBAC contenha as IDs de assinatura explícitos no local em que ela é usada. As IDs de assinatura são listadas sob o **AssignableScopes**, caso contrário, você não poderá importar a função na sua assinatura.

Depois de criar e personalizar a função RBAC, será preciso importá-la de volta o ambiente.

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

Neste exemplo, o nome personalizado para essa função RBAC é "Nível de acesso de tíquetes de suporte do Leitor" que permite ao usuário exibir tudo na assinatura e também abrir solicitações de suporte.

> [!NOTE]
> As duas únicas funções de RBAC internas que permitem a ação de abrir solicitações de suporte são **Proprietário** e **Colaborador**. Para um usuário poder abrir solicitações de suporte, ele deve receber uma função de RBAC apenas no escopo de assinatura, porque todas as solicitações de suporte são criadas com base em uma assinatura do Azure.

Essa nova função personalizada foi atribuída a um usuário do mesmo diretório.





![captura de tela de função personalizada de RBAC importada no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![captura de tela da atribuição de função de RBAC personalizada importada ao usuário no mesmo diretório](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![captura de tela de permissões para a função de RBAC personalizada importada](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

O exemplo foi totalmente detalhado para enfatizar os limites dessa função de RBAC personalizada da seguinte maneira:
* Pode criar novas solicitações de suporte
* Não pode criar novos escopos de recursos (por exemplo: máquina virtual)
* Não pode criar novos grupos de recursos





![captura de tela de função personalizada de RBAC criando solicitações de suporte](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![captura de tela de função de RBAC personalizada que não pode criar VMs](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![captura de tela de função de RBAC personalizada que não pode criar novos RGs](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Crie uma função de RBAC personalizada para abrir as solicitações de suporte usando a CLI do Azure
Na execução em Mac e sem acesso ao PowerShell, a CLI do Azure é a melhor opção.

As etapas para criar uma função personalizada são as mesmas, sendo a única exceção que, ao usar a CLI, a função não pode ser baixada em um modelo JSON, mas pode ser exibida na CLI.

Para este exemplo, escolhi a função interna de **Leitor de Backup**.

```

azure role show "backup reader" --json

```





![Captura de tela da CLI da função de leitor de backup mostrada](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

Editando a função no Visual Studio depois de copiar as propriedades em um modelo JSON, o provedor de recursos **Microsoft.Support** foi adicionado nas seções **Ações** para que esse usuário possa abrir solicitações de suporte enquanto continua sendo um leitor para os cofres de backup. Novamente, é preciso adicionar a ID de assinatura em que essa função será usada na seção **AssignableScopes**.

```

azure role create --inputfile <path>

```





![Captura de tela da CLI da importação da função de RBAC personalizada](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

A nova função agora está disponível no portal do Azure e o processo de atribuição é o mesmo que nos exemplos anteriores.





![Captura de tela do portal do Azure da função RBAC personalizada criada usando a CLI 1.0](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

Do Build de 2017 mais recente em diante, o Azure Cloud Shell está disponível ao público geral. O Azure Cloud Shell é um complemento ao IDE e do Portal do Azure. Com esse serviço, você obtém um shell baseado em navegador que é autenticado e hospedado no Azure e pode ser usado no lugar da CLI instalada em seu computador.





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)


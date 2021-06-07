---
title: 'Tutorial: Configurar o write-back do SAP SuccessFactors no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o write-back de atributos do SAP SuccessFactors no Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3260787dec4ae26cd6ef7cc3bd562f39db8e3655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526968"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Tutorial: Configurar o write-back de atributos do Azure AD para o SAP SuccessFactors
O objetivo deste tutorial é mostrar as etapas para fazer write-back de atributos do Azure AD para o SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Visão geral

Você pode configurar o aplicativo de write-back do SAP SuccessFactors para gravar atributos específicos do Azure Active Directory para o SAP SuccessFactors Employee Central. O aplicativo de provisionamento de write-back do SuccessFactors dá suporte à atribuição de valores aos seguintes atributos do Employee Central:

* Email comercial
* Nome de Usuário
* Número de telefone comercial (incluindo código do país, código de área, número e ramal)
* Sinalizador principal de número de telefone comercial
* Número de celular (incluindo código do país, código de área e número)
* Sinalizador principal de telefone celular 
* Atributos do usuário custom01-custom15
* Atributo loginMethod

> [!NOTE]
> Este aplicativo não tem nenhuma dependência dos aplicativos de integração de provisionamento de usuário de entrada do SuccessFactors. Você pode configurá-lo de maneira independente do aplicativo de provisionamento do [SuccessFactors para o AD local](sap-successfactors-inbound-provisioning-tutorial.md) e do aplicativo de provisionamento do [SuccessFactors para o Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Essa solução de provisionamento de usuário de Write-back do SuccessFactors é ideal para:

* As organizações que usam o Microsoft 365 que desejam fazer write-back de atributos autoritativos gerenciados pela TI (como endereço de email, nome de usuário e telefone) no SuccessFactors Employee Central.

## <a name="configuring-successfactors-for-the-integration"></a>Configurar o SuccessFactors para a integração

Todos os conectores de provisionamento do SuccessFactors exigem credenciais de uma conta do SuccessFactors com as permissões certas para invocar as APIs de OData do Employee Central. Esta seção descreve as etapas para criar a conta de serviço no SuccessFactors e conceder as permissões apropriadas. 

* [Criar/identificar a conta de usuário de API no SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar uma função de permissões de API](#create-an-api-permissions-role)
* [Criar um Grupo de Permissões para o usuário da API](#create-a-permission-group-for-the-api-user)
* [Conceder a Função de Permissão ao Grupo de Permissões](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar a conta de usuário de API no SuccessFactors
Trabalhe com a equipe de administradores ou o parceiro de implementação do SuccessFactors para criar ou identificar uma conta de usuário no SuccessFactors que será usada para invocar as APIs do OData. As credenciais de nome de usuário e senha dessa conta serão necessárias quando você configurar os aplicativos de provisionamento no Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar uma função de permissões de API

1. Faça logon no SAP SuccessFactors com uma conta de usuário que tenha acesso ao Centro de Administração.
1. Pesquise *Gerenciar Funções de Permissão* e selecione **Gerenciar Funções de Permissão** nos resultados da pesquisa.

   ![Gerenciar Funções de Permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Na Lista de Funções de Permissão, clique em **Criar**.

   > [!div class="mx-imgBorder"]
   > ![Criar Função de Permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Adicione um **Nome de Função** e uma **Descrição** para a função de permissão. O nome e a descrição devem indicar que a função é para permissões de uso da API.

   > [!div class="mx-imgBorder"]
   > ![Detalhe da função de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Em Configurações de permissão, clique em **Permissão...** e role para baixo até a lista de permissões e clique em **Gerenciar Ferramentas de Integração**. Marque a caixa de seleção para **Permitir que o Administrador Acesse a API do OData por meio da Autenticação Básica**.

   > [!div class="mx-imgBorder"]
   > ![Gerenciar ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Role para baixo na mesma caixa e selecione **API do Employee Central**. Adicione permissões conforme mostrado abaixo para ler usando a API do ODATA e editar usando a API do ODATA. Selecione a opção Editar se você planejar usar a mesma conta para o cenário de write-back para SuccessFactors. 

   > [!div class="mx-imgBorder"]
   > ![Permissões de leitura/gravação](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Clique em **Concluído**. Clique em **Salvar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um Grupo de Permissões para o usuário da API

1. No Centro de Administração do SuccessFactors, pesquise *Gerenciar Grupos de Permissões* e selecione **Gerenciar Grupos de Permissões** nos resultados da pesquisa.

   > [!div class="mx-imgBorder"]
   > ![Gerenciar grupos de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Na janela Gerenciar Grupos de Permissões, clique em **Criar**.

   > [!div class="mx-imgBorder"]
   > ![Adicione o novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Adicione um Nome do Grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para usuários de API.

   > [!div class="mx-imgBorder"]
   > ![Nome do grupo de permissões](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Adicione membros ao grupo. Por exemplo, você pode selecionar **Nome de usuário** no menu suspenso Pool de Pessoas e inserir o nome de usuário da conta de API que será usada para a integração. 

   > [!div class="mx-imgBorder"]
   > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Clique em **Concluído** para concluir a criação do Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Conceder a Função de Permissão ao Grupo de Permissões

1. No Centro de Administração do SuccessFactors, pesquise *Gerenciar Funções de Permissão* e selecione **Gerenciar Funções de Permissão** nos resultados da pesquisa.
1. Na **Lista da Função de Permissão**, selecione a função que você criou para permissões de uso de API.
1. Em **Conceder essa função a...** , clique no botão **Adicionar...** .
1. Selecione **Grupo de Permissões...** no menu suspenso e clique em **Selecionar...** para abrir a janela Grupos para pesquisar e selecionar o grupo criado acima. 

   > [!div class="mx-imgBorder"]
   > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Examine a concessão da Função de Permissão ao Grupo de Permissões. 
   > [!div class="mx-imgBorder"]
   > ![Função de Permissão e Detalhe do grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Clique em **Salvar Alterações**.

## <a name="preparing-for-successfactors-writeback"></a>Preparando-se para o Write-back do SuccessFactors

O aplicativo de provisionamento de Write-back do SuccessFactors usa determinados valores de *código* para definir o email e os números de telefone no Employee Central. Esses valores de *código* são definidos como valores constantes na tabela de mapeamento de atributos e são diferentes para cada instância do SuccessFactors. Esta seção fornece etapas para capturar esses valores de *código*.

   > [!NOTE]
   > Envolva o Administrador do SuccessFactors para concluir as etapas nesta seção. 

### <a name="identify-email-and-phone-number-picklist-names"></a>Identificar os nomes na lista de seleção de Email e Número de telefone 

No SAP SuccessFactors, uma *lista de seleção* é um conjunto configurável de opções entre as quais o usuário pode fazer uma seleção. Os diferentes tipos de email e número de telefone (por exemplo, comercial, pessoal e outros) são representados usando uma lista de seleção. Nesta etapa, identificaremos as listas de seleção configuradas em seu locatário do SuccessFactors para armazenar os valores de email e número de telefone. 
 
1. No Centro de Administração do SuccessFactors, pesquise *Gerenciar configuração de negócios*. 

   > [!div class="mx-imgBorder"]
   > ![Gerenciar configuração de negócios](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. Em **Elementos de HRIS**, selecione **emailInfo** e clique nos *Detalhes* no campo **tipo de email**.

   > [!div class="mx-imgBorder"]
   > ![Obter informações de email](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. Na página de detalhes do **tipo de email**, anote o nome da lista de seleção associada ao campo. Por padrão, ele fica em **ecEmailType**. No entanto, ele pode ser diferente em seu locatário. 

   > [!div class="mx-imgBorder"]
   > ![Identificar lista de seleção de email](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. Em **Elementos de HRIS**, selecione **phoneInfo** e clique nos *Detalhes* no campo **tipo de telefone**.

   > [!div class="mx-imgBorder"]
   > ![Obter informações de telefone](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. Na página de detalhes do **tipo de telefone**, anote o nome da lista de seleção associada ao campo. Por padrão, ele fica em **ecPhoneType**. No entanto, ele pode ser diferente em seu locatário. 

   > [!div class="mx-imgBorder"]
   > ![Identificar lista de seleção de telefone](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>Recuperar valor constante para emailType

1. No Centro de Administração do SuccessFactors, pesquise *Centro de Lista de Seleção* e abra-o. 
1. Use o nome da lista de seleção de email capturado na seção anterior (por exemplo, ecEmailType) para localizar a lista de seleção de email. 

   > [!div class="mx-imgBorder"]
   > ![Localizar lista de seleção de email](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. Abra a lista de seleção de email ativa. 

   > [!div class="mx-imgBorder"]
   > ![Abrir lista de seleção de tipo de email ativa](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. Na página da lista de seleção de tipo de email, selecione o tipo de email *Empresarial*.

   > [!div class="mx-imgBorder"]
   > ![Selecionar tipo de email empresarial](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. Tome nota da **ID da Opção** associada ao email *Empresarial*. Este é o código que usaremos com *emailType* na tabela de mapeamento de atributos.

   > [!div class="mx-imgBorder"]
   > ![Obter código de tipo de email](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > Remova o caractere de vírgula ao copiar o valor. Por exemplo, se o valor da **ID da Opção** for *8,448*, defina o *emailType* no Azure AD como o número constante *8448* (sem o caractere de vírgula). 

### <a name="retrieve-constant-value-for-phonetype"></a>Recuperar valor constante para phoneType

1. No Centro de Administração do SuccessFactors, pesquise *Centro de Lista de Seleção* e abra-o. 
1. Use o nome da lista de seleção de telefone capturado na seção anterior para localizar a lista de seleção de telefone. 

   > [!div class="mx-imgBorder"]
   > ![Localizar lista de seleção de tipo de telefone](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. Abra a lista de seleção de telefone ativa. 

   > [!div class="mx-imgBorder"]
   > ![Abrir lista de seleção de tipo de telefone ativa](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. Na página de lista de seleção de tipo de telefone, examine os diferentes tipos de telefone listados em **Valores da Lista de Seleção**.

   > [!div class="mx-imgBorder"]
   > ![Examinar os tipos de telefone](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. Tome nota da **ID da Opção** associada ao telefone *Empresarial*. Este é o código que usaremos com *businessPhoneType* na tabela de mapeamento de atributos.

   > [!div class="mx-imgBorder"]
   > ![Obter código do telefone comercial](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. Tome nota da **ID da Opção** associada ao telefone *Celular*. Este é o código que usaremos com *cellPhoneType* na tabela de mapeamento de atributos.

   > [!div class="mx-imgBorder"]
   > ![Obter código de telefone celular](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > Remova o caractere de vírgula ao copiar o valor. Por exemplo, se o valor da **ID da Opção** for *10,606*, defina o *cellPhoneType* no Azure AD como o número constante *10606* (sem o caractere de vírgula). 


## <a name="configuring-successfactors-writeback-app"></a>Configurando o Aplicativo de Write-back do SuccessFactors

Esta seção fornece as etapas para 

* [Adicionar o aplicativo de conector de provisionamento e configurar a conectividade com o SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicionar o aplicativo de conector de provisionamento e configurar a conectividade com o SuccessFactors

**Para configurar o Write-back do SuccessFactors:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5. Pesquise **Write-back do SuccessFactors** e adicione esse aplicativo da galeria.

6. Após adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**

7. Altere o **Modo** de **Provisionamento** para **Automático**

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário Administrador**: insira o nome de usuário da conta de usuário da API do SuccessFactors, com a ID da empresa acrescentada. Ele tem o formato: **nome de usuário\@IDdaEmpresa**

   * **Senha do administrador**: insira a senha da conta de usuário da API do SuccessFactors. 

   * **URL do Locatário**: insira o nome do ponto de extremidade de serviço da API do OData do SuccessFactors. Insira apenas o nome do host do servidor sem http ou https. Esse valor deve ser semelhante a `api4.successfactors.com`.

   * **Email de Notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.
    > [!NOTE]
    > O Serviço de Provisionamento do Azure AD envia uma notificação por email quando o trabalho de provisionamento entra no estado de [quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique novamente se as credenciais e a URL do SuccessFactors são válidas.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Após as credenciais serem salvas com êxito, a seção **Mapeamentos** exibirá o mapeamento padrão. Atualize a página se os mapeamentos de atributos não estiverem visíveis.  

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta seção, você vai configurar como o fluxo de dados do usuário flui do SuccessFactors para o Active Directory.

1. Na guia Provisionamento, em **Mapeamentos**, clique em **Provisionar Usuários do Azure Active Directory**.

1. No campo **Escopo do Objeto de Origem**, é possível selecionar quais conjuntos de usuários no Azure AD devem ser considerados para write-back definindo um conjunto de filtros baseados em atributo. O escopo padrão é “todos os usuários no Azure AD”. 
   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento de aplicativo pela primeira vez, você precisará testar e verificar seus mapeamentos de atributo e expressões para certificar-se de que ele está dando a você o resultado desejado. A Microsoft recomenda usar os filtros de escopo sob **Escopo do objeto de origem** para testar seus mapeamentos com alguns usuários de teste do Azure AD. Após ter verificado que os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. O campo **Ações do Objeto de Destino** dá suporte apenas à operação de **Atualização**.

1. Na tabela de mapeamento na seção **Mapeamentos de atributos**, você pode mapear os atributos a seguir do Azure Active Directory para o SuccessFactors. A tabela a seguir fornece orientações sobre como mapear os atributos de write-back. 

   | \# | Atributo do Azure AD | Atributo do SuccessFactors | Comentários |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Por padrão, esse atributo é o identificador correspondente. Em vez de employeeId, você pode usar qualquer outro atributo do Azure AD que possa armazenar o valor igual a personIdExternal no SuccessFactors.    |
   | 2 | mail | email | Mapear a origem do atributo de email. Para fins de teste, você pode mapear userPrincipalName para email. |
   | 3 | 8448 | emailType | Esse valor constante é o valor da ID do SuccessFactors associada ao email empresarial. Atualize esse valor de maneira a corresponder ao seu ambiente do SuccessFactors. Confira a seção [Recuperar o valor constante de emailType](#retrieve-constant-value-for-emailtype) para obter as etapas para definir esse valor. |
   | 4 | true | emailIsPrimary | Use este atributo para definir o email empresarial como o principal no SuccessFactors. Se o email empresarial não for o principal, defina esse sinalizador como falso. |
   | 5 | userPrincipalName | [custom01 – custom15] | Usando **Adicionar Novo Mapeamento**, você pode gravar userPrincipalName ou qualquer atributo do Azure AD como um atributo personalizado disponível no objeto de Usuário do SuccessFactors.  |
   | 6 | SamAccountName local | Nome de Usuário | Usando **Adicionar Novo Mapeamento**, você pode mapear samAccountName local para o atributo de nome de usuário do SuccessFactors. Use [Sincronização do Azure AD Connect: extensões de diretório](../hybrid/how-to-connect-sync-feature-directory-extensions.md) para sincronizar o samAccountName com o Azure AD. Ele será exibido no menu suspenso de origem como *extension_yourTenantGUID_samAccountName* |
   | 7 | SSO | loginMethod | Se o locatário do SuccessFactors for configurado para [SSO parcial](https://apps.support.sap.com/sap/support/knowledge/en/2320766), usando Adicionar Novo Mapeamento você poderá definir loginMethod como um valor constante de "SSO" ou "PWD". |
   | 8 | telephoneNumber | businessPhoneNumber | Use este mapeamento para transmitir o *telephoneNumber* do Azure AD para o número de telefone comercial/de trabalho do SuccessFactors. |
   | 9 | 10605 | businessPhoneType | Esse valor constante é o valor da ID do SuccessFactors associada ao telefone comercial. Atualize esse valor de maneira a corresponder ao seu ambiente do SuccessFactors. Confira a seção [Recuperar o valor constante de phoneType](#retrieve-constant-value-for-phonetype) para obter as etapas para definir esse valor. |
   | 10 | true | businessPhoneIsPrimary | Use este atributo para definir o sinalizador primário para o número de telefone comercial. Os valores válidos são true ou false. |
   | 11 | Serviço Móvel | cellPhoneNumber | Use este mapeamento para transmitir o *telephoneNumber* do Azure AD para o número de telefone comercial/de trabalho do SuccessFactors. |
   | 12 | 10606 | cellPhoneType | Esse valor constante é o valor da ID do SuccessFactors associada ao telefone celular. Atualize esse valor de maneira a corresponder ao seu ambiente do SuccessFactors. Confira a seção [Recuperar o valor constante de phoneType](#retrieve-constant-value-for-phonetype) para obter as etapas para definir esse valor. |
   | 13 | false | cellPhoneIsPrimary | Use este atributo para definir o sinalizador primário para o número de telefone celular. Os valores válidos são true ou false. |
 
1. Valide e examine os mapeamentos de atributos. 
 
    >[!div class="mx-imgBorder"]
    >![Mapeamentos de atributos de write-back](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Clique em **Salvar** para salvar os mapeamentos. Em seguida, atualizaremos as expressões da API de Caminho JSON para usar os códigos de phoneType em sua instância do SuccessFactors. 
1. Selecione **Mostrar opções avançadas**. 

    >[!div class="mx-imgBorder"]
    >![Mostrar opções avançadas](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Clique em **Editar lista de atributos do SuccessFactors**. 

   > [!NOTE] 
   > Se a opção **Editar lista de atributos para o SuccessFactors** não aparecer no portal do Azure, use a URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* para acessar a página. 

1. A coluna **Expressão da API** nessa exibição mostra as expressões de Caminho JSON usadas pelo conector. 
1. Atualize as expressões de Caminho JSON do telefone comercial e do telefone celular para usar o valor da ID (*businessPhoneType* e *cellPhoneType*) correspondente ao seu ambiente. 

    >[!div class="mx-imgBorder"]
    >![Alterar Caminho JSON do telefone](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Clique em **Salvar** para salvar os mapeamentos.

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros em problemas de dados de mapeamento, o trabalho de provisionamento poderá falhar e prosseguir para o estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

1. Selecione **Escopo**. É possível selecionar entre uma das seguintes opções: 
   * **Sincronizar todos os usuários e grupos**: selecione essa opção se você planejar fazer write-back dos atributos mapeados de todos os usuários do Azure AD para o SuccessFactors, sujeito às regras de escopo definidas em **Mapeamentos** -> **Escopo do Objeto de Origem**. 
   * **Sincronizar somente usuários e grupos atribuídos**: selecione esta opção se você planejar fazer write-back dos atributos mapeados somente dos usuários que você atribuiu a esse aplicativo na opção de menu **Aplicativo** -> **Gerenciar** -> **Usuários e grupos**. Esses usuários também estão sujeitos às regras de escopo definidas em **Mapeamentos** -> **Escopo do Objeto de Origem**.

   > [!div class="mx-imgBorder"]
   > ![Selecionar escopo de Write-back](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > O aplicativo de provisionamento de write-back SuccessFactors não dá suporte à "atribuição de grupo". Há suporte apenas para a "atribuição de usuário". 

1. Clique em **Salvar**.

1. Essa operação dará início à sincronização inicial, o que poderá demorar algumas horas dependendo de quantos usuários estiverem no locatário do Azure AD e do escopo definido para a operação. Verifique a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

1. A qualquer momento, verifique a guia **Logs de provisionamento** no portal do Azure para ver as ações executadas pelo serviço de provisionamento. Os logs de provisionamento listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento. 

1. Após a sincronização inicial ser concluída, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Barra de progresso do provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Cenários com suporte, problemas conhecidos e limitações

Confira a [Seção de cenários de write-back](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) do guia de referência de integração do SAP SuccessFactors. 

## <a name="next-steps"></a>Próximas etapas

* [Aprofunde-se na referência de integração do Azure AD e do SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o SuccessFactors e o Azure Active Directory](successfactors-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)
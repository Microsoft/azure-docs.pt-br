---
title: 'Tutorial: Configurar o provisionamento de entrada do SuccessFactors no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada do SuccessFactors para o Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a39f7701d0244eff7f914908a6708dfc14873b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954847"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Tutorial: Configurar o SAP SuccessFactors para o provisionamento de usuário do Azure AD
O objetivo deste tutorial é mostrar as etapas que você precisa seguir para provisionar dados de trabalho do SuccessFactors Employee Central para o Azure Active Directory, com write-back opcional de endereço de email para o SuccessFactors. 

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do SuccessFactors forem usuários somente de nuvem que não precisam de uma conta do AD local. Se os usuários precisarem apenas de uma conta do AD local ou de uma conta do AD e do Azure AD, veja o tutorial de como [configurar o SAP SuccessFactors para o provisionamento de usuário do Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview). 

## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuário do Azure Active Directory](../app-provisioning/user-provisioning.md) integra-se ao [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerenciar o ciclo de vida de identidade dos usuários. 

Os fluxos de trabalho de provisionamento de usuário do SuccessFactors com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento do ciclo de vida de identidades e recurso humanos:

* **Contratação de novos funcionários**: quando um novo funcionário é adicionado ao SuccessFactors, uma conta de usuário é criada automaticamente no Azure Active Directory e, opcionalmente, no Microsoft 365 e em [outros aplicativos SaaS compatíveis com o Azure AD](../app-provisioning/user-provisioning.md), com write-back do endereço de email para o SuccessFactors.

* **Atualizações de perfil e atributo de funcionário**: quando um registro de funcionário for atualizado no SuccessFactors (como nome, cargo ou gerente), a conta de usuário dele será atualizada automaticamente no Azure Active Directory e, opcionalmente, no Microsoft 365 e em [outros aplicativos SaaS compatíveis com o Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões de funcionário**: quando é feita a rescisão de um funcionário no SuccessFactors, a conta de usuário dele é desabilitada automaticamente no Azure Active Directory e, opcionalmente, no Microsoft 365 e em [outros aplicativos SaaS compatíveis com o Azure AD](../app-provisioning/user-provisioning.md).

* **Recontratação de funcionário**: quando um funcionário é recontratado no SuccessFactors, a conta antiga do funcionário pode ser reativada ou reprovisionada automaticamente (dependendo da sua preferência) no Azure Active Directory e, opcionalmente, no Microsoft 365 e em [outros aplicativos SaaS compatíveis com o Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Essa solução de provisionamento de usuário do SuccessFactors para o Azure Active Directory é ideal para:

* Organizações que desejam uma solução predefinida e baseada em nuvem para o provisionamento de usuário do SuccessFactors

* Organizações que precisam de provisionamento de usuário direto do SuccessFactors para o Azure Active Directory

* Organizações que precisam que os usuários sejam provisionados usando os dados obtidos do [SuccessFactors EC (Employee Central)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Microsoft 365 para email

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção descreve a arquitetura da solução de provisionamento do usuário de ponta a ponta para usuários somente de nuvem. Há dois fluxos relacionados:

* **Fluxo de Dados de RH Autoritativo: do SuccessFactors para o Azure Active Directory:** nesse fluxo, os eventos de trabalho (como Novas Contratações, Transferências, Demissões) ocorrem primeiro no SuccessFactors Employee Central da nuvem e os dados do evento fluem para o Azure Active Directory. Dependendo do evento, pode levar a operações de criar/atualizar/habilitar/desabilitar no Azure AD.
* **Fluxo de Write-Back de Email – do Active Directory local para o SuccessFactors:** depois que a criação da conta for concluída no Azure Active Directory, o valor do atributo de email ou o UPN gerado no Azure AD poderá ser gravado novamente no SuccessFactors.

  ![Visão geral](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH realiza transações de trabalho (adições/transferências/saídas ou novas contratações/transferências/demissões) no SuccessFactors Employee Central
2. O Serviço de Provisionamento do Azure AD executa sincronizações agendadas de identidades do SuccessFactors EC e identifica alterações que precisam ser processadas para a sincronização com o Active Directory local.
3. O Serviço de Provisionamento do Azure AD determina a alteração e invoca uma operação criar/atualizar/habilitar/desabilitar para o usuário no Azure AD.
4. Se o [aplicativo de write-back do SuccessFactors](sap-successfactors-writeback-tutorial.md) for configurado, o endereço de email do usuário será recuperado do Azure AD. 
5. O serviço de provisionamento do Azure AD faz write-back do atributo de email no SuccessFactors com base no atributo correspondente usado.

## <a name="planning-your-deployment"></a>Planejamento da implantação

A configuração do provisionamento de usuário controlado pelo RH na Nuvem do SuccessFactors para o Azure AD exige um planejamento considerável que aborda diferentes aspectos como:

* Determinar a ID de correspondência 
* Mapeamento de atributos
* Transformação de atributos 
* Filtros de escopo

Veja o [plano de implantação do RH na Nuvem](../app-provisioning/plan-cloud-hr-provision.md) para obter diretrizes abrangentes sobre esses tópicos. Veja a [referência de integração do SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) para saber mais sobre as entidades com suporte, os detalhes de processamento e como personalizar a integração para diferentes cenários de RH. 

## <a name="configuring-successfactors-for-the-integration"></a>Configurar o SuccessFactors para a integração

Um requisito comum de todos os conectores de provisionamento do SuccessFactors é que eles exigem credenciais de uma conta do SuccessFactors com as permissões certas para invocar as APIs do OData do SuccessFactors. Esta seção descreve as etapas para criar a conta de serviço no SuccessFactors e conceder as permissões apropriadas. 

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
1. Role para baixo na mesma caixa e selecione **API do Employee Central**. Adicione permissões conforme mostrado abaixo para ler usando a API do ODATA e editar usando a API do ODATA. Selecione a opção Editar se você planejar usar a mesma conta para o cenário de Write-back para SuccessFactors. 
    > [!div class="mx-imgBorder"]
    > ![Permissões de leitura/gravação](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Na mesma caixa de permissões, acesse **Permissões de Usuário -> Dados de Funcionário** e examine os atributos que a conta de serviço pode ler do locatário SuccessFactors. Por exemplo, para recuperar o atributo *Nome de usuário* de SuccessFactors, verifique se a permissão "Exibição" é concedida para esse atributo. Da mesma forma, examine cada atributo para obter a permissão de exibição. 

    > [!div class="mx-imgBorder"]
    > ![Permissões de dados de funcionário](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >Para obter a lista completa de atributos recuperados por esse aplicativo de provisionamento, veja [Referência de Atributos do SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Configurar o provisionamento de usuário do SuccessFactors para o Azure AD

Esta seção fornece as etapas para o provisionamento de contas de usuário do SuccessFactors para o Azure AD.

* [Adicionar o aplicativo de conector de provisionamento e configurar a conectividade com o SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicionar o aplicativo de conector de provisionamento e configurar a conectividade com o SuccessFactors

**Para configurar o SuccessFactors para o provisionamento do Azure AD:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5. Pesquise **Provisionamento de Usuário do SuccessFactors para o Azure Active Directory** e adicione esse aplicativo da galeria.

6. Após adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**

7. Altere o **Modo** de **Provisionamento** para **Automático**

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário Administrador**: insira o nome de usuário da conta de usuário da API do SuccessFactors, com a ID da empresa acrescentada. Ele tem o formato: **nome de usuário\@IDdaEmpresa**

   * **Senha do administrador**: insira a senha da conta de usuário da API do SuccessFactors. 

   * **URL do Locatário**: insira o nome do ponto de extremidade de serviço da API do OData do SuccessFactors. Insira apenas o nome do host do servidor sem http ou https. Esse valor deve ser semelhante a: **nome-do-servidor-de-api.successfactors.com**.

   * **Email de Notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.
    > [!NOTE]
    > O Serviço de Provisionamento do Azure AD envia uma notificação por email quando o trabalho de provisionamento entra no estado de [quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique novamente se as credenciais e a URL do SuccessFactors são válidas.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Depois que as credenciais são salvas com êxito, a seção **Mapeamentos** exibirá o mapeamento padrão **Sincronizar Usuários do SuccessFactors com o Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta seção, você vai configurar como o fluxo de dados do usuário flui do SuccessFactors para o Active Directory.

1. Na guia Provisionamento, em **Mapeamentos**, clique em **Sincronizar Usuários do SuccessFactors com o Azure Active Directory**.

1. No campo **Escopo do Objeto de Origem**, é possível selecionar quais conjuntos de usuários no SuccessFactors devem estar no escopo de provisionamento para o Azure AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é “todos os usuários no Azure SuccessFactors”. Filtros de exemplo:

   * Exemplo: O escopo para usuários com personIdExternal entre 1000000 e 2000000 (excluindo 2000000)

      * Atributo: personIdExternal

      * Operador: Coincidir EXREG

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas funcionários e trabalhadores não contingentes

      * Atributo: EmployeeID

      * Operador: IS NOT NULL

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento de aplicativo pela primeira vez, você precisará testar e verificar seus mapeamentos de atributo e expressões para certificar-se de que ele está dando a você o resultado desejado. A Microsoft recomenda o uso de filtros de escopo em **Escopo do Objeto de Origem** para testar seus mapeamentos com alguns usuários de teste do SuccessFactors. Após ter verificado que os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários fora do escopo. Isso pode não ser desejável para sua integração do SuccessFactors ao Azure AD. Para substituir esse comportamento padrão, consulte o artigo [Ignorar a exclusão de contas de usuário fora do escopo](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Ações do objeto de destino** é possível filtrar globalmente quais ações são executadas no Active Directory. **Criar** e **Atualizar** são as mais comuns.

1. Na seção **Mapeamentos de atributos**, é possível definir como os atributos individuais do SuccessFactors são mapeados para os atributos do Active Directory.

     >[!NOTE]
     >Para obter a lista completa de atributos do SuccessFactors com suporte do aplicativo, veja [Referência de Atributos do SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

1. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual dá suporte para essas propriedades:

      * **Tipo de Mapeamento**

         * **Direto** – grava o valor do atributo do SuccessFactors no atributo do AD, sem alterações

         * **Constante** - grava um valor de cadeia de caracteres constante estático para o atributo do AD

         * **Expressão** – permite gravar um valor personalizado para o atributo do AD baseado em um ou mais atributos do SuccessFactors. [Para obter mais informações, consulte este artigo sobre expressões](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atributo de origem** – o atributo de usuário do SuccessFactors

      * **Valor padrão** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento irá gravar esse valor.
            A configuração mais comum é deixar em branco.

      * **Atributo de destino** –  o atributo de usuário no Active Directory.

      * **Corresponder objetos utilizando esse atributo** – se esse mapeamento deverá ou não ser usado para identificar usuários entre o SuccessFactors e o Active Directory com exclusividade. Normalmente, esse valor é definido no campo ID do Trabalhador para o SuccessFactors, que geralmente é mapeado para um dos atributos da ID do Funcionário no Active Directory.

      * **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum outro atributo correspondente será avaliado.

      * **Aplicar esse mapeamento**

         * **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário

         * **Somente durante a criação** – aplicar esse mapeamento somente nas ações de criação de usuário

1. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.

Uma vez concluída a configuração do mapeamento de atributo, você pode agora [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros em problemas de dados de mapeamento ou Workday, o trabalho de provisionamento pode falhar e prosseguir para o estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

2. Clique em **Save** (Salvar).

3. Essa operação dará início à sincronização inicial, o que poderá demorar algumas horas dependendo de quantos usuários estiverem no locatário do SuccessFactors. Verifique a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização realizados pelo serviço de provisionamento, tais como aqueles em que os usuários estão sendo lidos do Workday e, posteriormente adicionados ou atualizados no Active Directory. 

5. Após a sincronização inicial ser concluída, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Barra de progresso do provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os Atributos do SuccessFactors com suporte para o provisionamento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar o write-back de email para o SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o SuccessFactors e o Azure Active Directory](successfactors-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)
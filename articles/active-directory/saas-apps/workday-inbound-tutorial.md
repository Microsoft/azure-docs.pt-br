---
title: 'Tutorial: Configurar o Workday para provisionamento automático do usuário com Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Active Directory para provisionar e descontinuar automaticamente as contas de usuário para o Workday.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a34881901fd8642fff9ac37512cd2ef260ad9d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954202"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Tutorial: Configurar o Workday para provisionamento automático do usuário

O objetivo deste tutorial é mostrar as etapas que você precisará executar para provisionar perfis de trabalho do Workday no AD (Active Directory) local.

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do Workday precisarem de uma conta do AD local e uma conta do Azure AD. 
>* Se os usuários do Workday só precisarem de uma conta do Azure AD (usuários somente de nuvem), veja o tutorial sobre como [configurar o Workday para o provisionamento de usuário do Azure AD](workday-inbound-cloud-only-tutorial.md). 
>* Para configurar o write-back de atributos como endereço de email, nome de usuário e número de telefone do Azure AD para o Workday, veja o tutorial sobre como [configurar o write-back do Workday](workday-writeback-tutorial.md).

## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuário do Azure Active Directory](../app-provisioning/user-provisioning.md) integra-se com a [API de Recursos Humanos do Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) para provisionar contas de usuários. Os fluxos de trabalho de provisionamento de usuário do Workday com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento do ciclo de vida de identidade e recurso humanos:

* **Contratação de novos funcionários**: quando um novo funcionário é adicionado ao Workday, uma conta de usuário é criada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Microsoft 365 e em [outros aplicativos SaaS compatíveis com o Azure AD](../app-provisioning/user-provisioning.md), com o write-back das informações de contato gerenciadas pela TI no para Workday.

* **Atualizações de perfil e atributo de funcionário**: quando um registro de funcionário for atualizado no Workday (como nome, cargo ou gerente), a conta de usuário dele será atualizada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Microsoft 365 e em [outros aplicativos SaaS compatíveis com o Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisão de funcionário**: quando é feita a rescisão de um funcionário no Workday, a conta de usuário dele é desabilitada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Microsoft 365 e em [outros aplicativos SaaS compatíveis com o Azure AD](../app-provisioning/user-provisioning.md).

* **Recontratação de funcionário**: quando um funcionário é recontratado no Workday, a conta antiga dele pode ser reativada ou reprovisionada automaticamente (dependendo da sua preferência) no Active Directory, no Azure Active Directory e, opcionalmente, no Microsoft 365 e em [outros aplicativos SaaS compatíveis com o Azure AD](../app-provisioning/user-provisioning.md).

### <a name="whats-new"></a>Novidades
Esta seção captura os aprimoramentos recentes de integração do Workday. Para obter uma lista completa das atualizações, das alterações planejadas e dos arquivos, acesse a página [Novidades do Azure Active Directory](../fundamentals/whats-new.md) 

* **Outubro de 2020 – Provisionamento habilitado sob demanda para o Workday:** Usando [provisionamento sob demanda](../app-provisioning/provision-on-demand.md), agora é possível testar o provisionamento de ponta a ponta para um perfil do usuário específico no Workday a fim de verificar o mapeamento de atributos e a lógica de expressão.   

* **Maio de 2020 – Capacidade de fazer write-back de números de telefone no Workday:** Além de emails e nomes de usuário, agora você pode fazer write-back de números de telefone comercial e de telefone celular do Azure AD para o Workday. Para obter mais detalhes, veja o [tutorial do aplicativo de write-back](workday-writeback-tutorial.md).

* **Abril de 2020 – Suporte à última versão da API do WWS (Workday Web Services):** Duas vezes por ano em março e setembro, o Workday fornece atualizações repletas de recursos que ajudam você a atender às suas metas empresariais e às demandas em constante mudança da força de trabalho. Para acompanhar os novos recursos fornecidos pelo Workday, agora você pode especificar diretamente a versão da API do WWS que deseja usar na URL de conexão. Para obter detalhes sobre como especificar a versão da API do Workday, veja a seção sobre [como configurar a conectividade do Workday](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory). 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Essa solução de provisionamento de usuário do Workday é ideal para:

* Organizações que desejam uma solução predefinida, baseados em nuvem para o provisionamento de usuário do Workday

* Organizações que precisam de provisionamento de usuário direto do Workday para o Active Directory ou Azure Active Directory

* Organizações que precisam que os usuários sejam provisionados usando os dados obtidos do módulo HCM do Workday (consulte [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* As organizações que precisam que os usuários adicionados, transferidos e excluídos sejam sincronizados com um ou mais florestas, domínios e unidades organizacionais do Active Directory com base somente em alterar informações detectadas no módulo HCM Workday (consulte [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizações que usam o Microsoft 365 para email

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção descreve a arquitetura da solução de provisionamento do usuário de ponta a ponta para ambientes híbridos comuns. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritativo – do Workday para o Active Directory local:** Nesse fluxo, os eventos de trabalho (como Novas Contratações, Transferências, Demissões) ocorrem primeiro no locatário de RH do Workday na nuvem e, em seguida, os dados do evento fluem para o Active Directory local por meio do Azure AD e do Agente de Provisionamento. Dependendo do evento, pode levar a operações de criar/atualizar/habilitar/desabilitar no AD.
* **Fluxo de write-back – do Active Directory local para o Workday:** Depois que a criação da conta for concluída no Active Directory, ela será sincronizada com o Azure AD por meio do Azure AD Connect e poderá ser feito o write-back de informações como email, nome de usuário e número de telefone no Workday.

![Visão geral](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH realiza transações de trabalho (acréscimos/movimentações/saídas ou novas contratações/transferências/desligamentos) no HCM do Workday
2. O Serviço de Provisionamento do Azure AD executa sincronizações agendadas de identidades de RH do Workday e identifica alterações que precisam ser processadas para sincronização com o Active Directory local.
3. O Serviço de Provisionamento do Azure AD invoca o Agente de Provisionamento do Azure AD Connect local com um conteúdo de solicitação que contém operações de criar/atualizar/habilitar/desabilitar contas do AD.
4. O Agente de Provisionamento do Azure AD Connect usa uma conta de serviço para adicionar/atualizar dados de conta do AD.
5. O mecanismo do Azure AD Connect/AD Sync executa uma sincronização delta para efetuar pull das atualizações no AD.
6. As atualizações do Active Directory são sincronizadas com o Azure Active Directory.
7. Se o aplicativo de [Write-back do Workday](workday-writeback-tutorial.md) estiver configurado, ele fará o write-back de atributos como email, nome de usuário e número de telefone no Workday.

## <a name="planning-your-deployment"></a>Planejamento da implantação

A configuração do Workday para o provisionamento de usuário do Active Directory exige um planejamento considerável que abranja diferentes aspectos, como:
* Configuração do agente de provisionamento do Azure AD Connect 
* Número de aplicativos de provisionamento de usuário do Workday para o AD a serem implantados
* Escolha do identificador, do mapeamento de atributos, além dos filtros de escopo e transformação correspondentes corretos

Veja o [plano de implantação de RH na nuvem](../app-provisioning/plan-cloud-hr-provision.md) para obter diretrizes abrangentes e as melhores práticas recomendadas. 

## <a name="configure-integration-system-user-in-workday"></a>Configurar o usuário do sistema de integração no Workday

Um requisito comum de todos os conectores de provisionamento do Workday é que eles exigem credenciais de um usuário do sistema de integração do Workday para conectar-se à API de Recursos Humanos do Workday. Esta seção descreve como criar um usuário do sistema de integração no Workday e possui as seguintes seções:

* [Criar um usuário do sistema de integração](#creating-an-integration-system-user)
* [Criar um grupo de segurança de integração](#creating-an-integration-security-group)
* [Configurar permissões de política de segurança do domínio](#configuring-domain-security-policy-permissions)
* [Configurar permissões da política de segurança de processo empresarial](#configuring-business-process-security-policy-permissions)
* [Ativar alterações de política de segurança](#activating-security-policy-changes)

> [!NOTE]
> É possível ignorar esse procedimento e utilizar uma conta Administrador global do Workday como a conta de integração do sistema. Isso pode funcionar aparentemente bem para demonstrações, mas não é recomendável para implementações de produção.

### <a name="creating-an-integration-system-user"></a>Criação de um usuário do sistema de integração

**Para criar um usuário do sistema de integração:**

1. Entre no seu locatário do Workday usando uma conta de administrador. No **Aplicativo Workday**, insira “criar usuário” na caixa de pesquisa e clique em **Criar Usuário do Sistema de Integração**.

   >[!div class="mx-imgBorder"] 
   >![Criar usuário](./media/workday-inbound-tutorial/wd_isu_01.png "Criar usuário")
2. Conclua a tarefa **Criar Usuário do Sistema de Integração** fornecendo um nome de usuário e senha para um novo Usuário do Sistema de Integração.  

   * Deixe a opção **Exigir Nova Senha na Próxima Entrada** desmarcada, porque esse usuário efetuará logon por meio de programação.
   * Deixe os **Minutos de Tempo Limite da Sessão** com seu valor padrão de 0, o que impedirá que as sessões do usuário expirem prematuramente.
   * Selecione a opção **Não Permitir Sessões de Interface do Usuário**, pois ela fornece uma camada adicional de segurança que impede que usuários com a senha do sistema de integração façam logon no Workday.

   > [!div class="mx-imgBorder"]
   > ![Criar Usuário do Sistema de Integração](./media/workday-inbound-tutorial/wd_isu_02.png "Criar Usuário do Sistema de Integração")

### <a name="creating-an-integration-security-group"></a>Criar um grupo de segurança de integração

Nesta etapa, você criará um grupo de segurança do sistema de integração irrestrito ou restrito no Workday e atribuirá o usuário do sistema de integração criado na etapa anterior a esse grupo.

**Para criar um grupo de segurança:**

1. Insira Criar grupo de segurança na caixa de pesquisa e clique em **Criar Grupo de Segurança**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela que mostra "criar grupo de segurança" inserido na caixa de pesquisa e a opção "Criar Grupo de Segurança – Tarefa" exibida nos resultados da pesquisa.](./media/workday-inbound-tutorial/wd_isu_03.png)
2. Conclua a tarefa **Criar Grupo de Segurança**. 

   * Há dois tipos de grupos de segurança no Workday:
     * **Irrestrito:** Todos os membros do grupo de segurança podem acessar todas as instâncias de dados protegidas pelo grupo de segurança.
     * **Restrito:** Todos os membros do grupo de segurança têm acesso contextual a um subconjunto das instâncias de dados (linhas) que o grupo de segurança pode acessar.
   * Entre em contato com seu parceiro de integração do Workday para selecionar o tipo de grupo de segurança adequado para a integração.
   * Após saber o tipo de grupo, selecione **Grupo de Segurança do Sistema de Integração (irrestrito)** ou **Grupo de Segurança do Sistema de Integração (restrito)** no menu suspenso **Tipo de Grupo de Segurança com Locatário**.

     > [!div class="mx-imgBorder"]
     >![Grupo CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "Grupo CreateSecurity")

3. Após a criação bem-sucedida do Grupo de segurança, você verá uma página na qual pode atribuir membros ao Grupo de segurança. Adicione o novo usuário do sistema de integração criado na etapa anterior a esse grupo de segurança. Se você estiver usando o grupo de segurança *restrito*, você também precisará selecionar o escopo da organização apropriado.

   >[!div class="mx-imgBorder"]
   >![Editar Grupo de Segurança](./media/workday-inbound-tutorial/wd_isu_05.png "Editar Grupo de Pesquisa")

### <a name="configuring-domain-security-policy-permissions"></a>Configurar permissões de política de segurança do domínio

Nesta etapa, você concederá permissões de política de “segurança de domínio” para os dados de trabalho ao grupo de segurança.

**Para configurar permissões de política de segurança do domínio:**

1. Insira **Associação de Grupo de Segurança e de Acesso** na caixa de pesquisa e clique no link de relatório.
   >[!div class="mx-imgBorder"]
   >![Pesquisar Associação de Grupo de Segurança](./media/workday-inbound-tutorial/security-group-membership-access.png)

1. Pesquise e selecione o grupo de segurança que você criou na etapa anterior. 
   >[!div class="mx-imgBorder"]
   >![Selecionar Grupo de Segurança](./media/workday-inbound-tutorial/select-security-group-workday.png)

1. Clique nas reticências (...) ao lado do nome do grupo e, no menu, selecione **Grupo de Segurança > Manter Permissões de Domínio Para o Grupo de Segurança**
   >[!div class="mx-imgBorder"]
   >![Selecione Manter Permissões de Domínio](./media/workday-inbound-tutorial/select-maintain-domain-permissions.png)

1. Em **Permissões de Integração**, adicione os domínios a seguir à lista **Políticas de Segurança de Domínio permitindo acesso Put**
   * *Provisionamento de conta externa*
   * *Dados do Trabalhador: Relatórios do Trabalhador Público* 
   * *Dados Pessoais: Informações de Contato de Trabalho* (necessário se você planeja fazer write-back de dados de contato do Azure AD para o Workday)
   * *Contas do Workday* (necessário se você planeja fazer write-back de nome de usuário/UPN do Azure AD para o Workday)

1. Em **Permissões de Integração**, adicione os domínios a seguir à lista **Políticas de Segurança de Domínio permitindo acesso Get**
   * *Dados do Trabalhador: Trabalhadores*
   * *Dados do Trabalhador: Todos os Cargos*
   * *Dados do Trabalhador: Informação da Equipe Atual*
   * *Dados do Trabalhador: Título Comercial no Perfil do Trabalhador*
   * *Dados do Trabalhador: Trabalhadores Qualificados* (opcional – adicione isso para recuperar dados de qualificação do trabalhador para provisionamento)
   * *Dados do Trabalhador: Habilidades e Experiência* (opcional – adicione isso para recuperar dados de habilidades do trabalhador para provisionamento)

1. Após a conclusão das etapas acima, a tela permissões será exibida como mostrado abaixo:
   >[!div class="mx-imgBorder"]
   >![Todas as Permissões de Segurança do Domínio](./media/workday-inbound-tutorial/all-domain-security-permissions.png)

1. Clique em **OK** e em **Concluído** na próxima tela para concluir a configuração. 

### <a name="configuring-business-process-security-policy-permissions"></a>Configurar permissões da política de segurança de processo empresarial

Nesta etapa, você concederá permissões de política de “segurança de processo empresarial” para os dados de trabalho ao grupo de segurança. 

> [!NOTE]
> Esta etapa só é necessária para configurar o conector de aplicativos de Write-back do Workday.

**Para configurar permissões da política de segurança de processo empresarial:**

1. Insira **Política de processo empresarial** na caixa de pesquisa e, em seguida, clique no link da tarefa **Editar Política de Segurança de Processo Empresarial**.  

   >[!div class="mx-imgBorder"]
   >![Captura de tela que mostra "Política de Processo Empresarial" na caixa de pesquisa e a opção "Editar Política de Segurança de Processo Empresarial – Tarefa" selecionada.](./media/workday-inbound-tutorial/wd_isu_12.png "Políticas de Segurança do Processo Empresarial")  

2. Na caixa de texto **Tipo de Processo Empresarial**, pesquise por *Contato*, selecione o processo empresarial **Mudar Contato Comercial** e clique em **OK**.

   >[!div class="mx-imgBorder"]
   >![Captura de tela que mostra a página "Editar Política de Segurança de Processo Empresarial" e a opção "Alteração de Contato Comercial" selecionada no menu "Tipo de Processo Empresarial".](./media/workday-inbound-tutorial/wd_isu_13.png "Políticas de Segurança do Processo Empresarial")  

3. Na página **Editar Política de Segurança do Processo Empresarial**, role até a seção **Manter Informações de Contato Comercial (serviço Web)** .


4. Selecione e adicione o novo grupo de segurança do sistema de integração à lista de grupos de segurança que podem iniciar a solicitação de serviços Web. 

   >[!div class="mx-imgBorder"]
   >![Políticas de Segurança do Processo Empresarial](./media/workday-inbound-tutorial/wd_isu_15.png "Políticas de Segurança do Processo Empresarial")  

5. Clique em **Concluído**. 

### <a name="activating-security-policy-changes"></a>Ativando alterações de política de segurança

**Para ativar alterações de política de segurança:**

1. Digite Ativar na caixa de pesquisa e, em seguida, clique no link **Ativar alterações de política de segurança pendentes**.
   >[!div class="mx-imgBorder"]
   >![Ativar](./media/workday-inbound-tutorial/wd_isu_16.png "Ativar")

1. Inicie a tarefa Ativar Alterações de Política de Segurança Pendentes inserindo um comentário para fins de auditoria e clique em **OK**.
1. Conclua a tarefa na próxima tela marcando a caixa de seleção **Confirmar** e clique em **OK**.

   >[!div class="mx-imgBorder"]
   >![Ativar Segurança Pendente](./media/workday-inbound-tutorial/wd_isu_18.png "Ativar segurança pendente")  

## <a name="provisioning-agent-installation-prerequisites"></a>Pré-requisitos de instalação do agente de provisionamento

Examine os [pré-requisitos de instalação do agente de provisionamento](../cloud-sync/how-to-prerequisites.md) antes de prosseguir para a próxima seção. 

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurando o provisionamento de usuário do Workday para o Active Directory

Esta seção fornece as etapas para o provisionamento de conta do usuário do Workday para cada domínio do Active Directory no escopo de sua integração.

* [Adicionar o aplicativo do conector de provisionamento e baixar o Agente de Provisionamento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalar e configurar Agentes de Provisionamento locais](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configurar a conectividade com o Workday e o Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Configurar mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e baixar o Agente de Provisionamento

**Para configurar o Workday para provisionamento do Active Directory:**

1. Ir para <https://portal.azure.com>.

2. Na portal do Azure, procure e selecione **Azure Active Directory**.

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5. Pesquise **Provisionamento de Usuário do Workday para o Active Directory** e adicione esse aplicativo por meio da galeria.

6. Depois de adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**.

7. Altere o **Modo** de **Provisionamento** para **Automático**.

8. Clique na faixa de informações exibida para baixar o Agente de Provisionamento. 

   >[!div class="mx-imgBorder"]
   >![Baixar Agente](./media/workday-inbound-tutorial/pa-download-agent.png "Tela Baixar Agente")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Instalar e configurar Agentes de Provisionamento local

Para provisionar no Active Directory local, o agente de Provisionamento deverá ser instalado em um servidor conectado ao domínio que tenha acesso à rede para os domínios do Active Directory desejados.

Transfira o instalador do agente baixado para o host do servidor e siga as etapas listadas [na seção **Instalar o agente**](../cloud-sync/how-to-install.md) para concluir a configuração do agente.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Parte 3: No aplicativo de provisionamento, configure a conectividade com o Workday e o Active Directory
Nesta etapa, estabelecemos a conectividade com o Workday e o Active Directory no portal do Azure. 

1. No portal do Azure, volte para o Provisionamento de Usuário do Workday para o Active Directory criado na [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário do Workday** – Digite o nome de usuário da conta do sistema de integração do Workday com o nome de domínio do locatário acrescentado. Ele deve ser semelhante a: **username\@tenant_name**

   * **Senha do Workday –** Digite a senha da conta do sistema de integração do Workday

   * **URL da API do Workday Web Services:** insira a URL para o ponto de extremidade de serviços Web do Workday para o seu locatário. A URL determina a versão da API do Workday Web Services usada pelo conector. 

     | Formato de URL | Versão usada da API do WWS | Alterações necessárias em XPATH |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | Não |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | Não |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v##.# | Sim |

      > [!NOTE]
     > Se nenhuma informação de versão for especificada na URL, o aplicativo usará o WWS (Workday Web Services) v21.1 e nenhuma alteração será necessária para as expressões padrão da API XPATH enviadas com o aplicativo. Para usar uma versão específica da API do WWS, especifique o número de versão na URL <br>
     > Exemplo: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Se você estiver usando uma API do WWS v30.0 e superior, antes de ativar o trabalho de provisionamento, atualize as **expressões da API XPATH** em **Mapeamento de Atributos -> Opções Avançadas -> Editar lista de atributos do Workday** vendo a seção [Como gerenciar sua configuração](#managing-your-configuration) e a [Referência de atributos do Workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **Floresta do Active Directory –** o “Nome” de seu domínio do Active Directory, conforme registrado com o agente. Use a lista suspensa para selecionar o domínio de destino para o provisionamento. Geralmente, esse valor é uma cadeia de caracteres como: *contoso.com*

   * **Contêiner do Active Directory –** insira o DN do contêiner em que o agente deve criar contas de usuário por padrão.
        Exemplo: *OU=Standard Users,OU=Users,DC=contoso,DC=test*

     > [!NOTE]
     > Essa configuração só entra em vigor para criações de contas de usuário quando o atributo *parentDistinguishedName* não está configurado nos mapeamentos de atributo. Essa configuração não é usada para operações de atualização e pesquisa de usuários. A subárvore de todo o domínio se enquadra no escopo da operação de pesquisa.

   * **Email de Notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.

     > [!NOTE]
     > O Serviço de Provisionamento do Azure AD envia uma notificação por email quando o trabalho de provisionamento entra no estado de [quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se ele falhar, verifique se as credenciais do Workday e as credenciais do AD configuradas na instalação de agente são válidas.

     >[!div class="mx-imgBorder"]
     >![Captura de tela que mostra a página de "Provisionamento" com as credenciais inseridas.](./media/workday-inbound-tutorial/wd_1.png)

   * Depois que as credenciais são salvas com êxito, a seção **Mapeamentos** exibirá o mapeamento padrão **Sincronizar trabalhadores do Workday com o Active Directory local**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: Configurar mapeamentos de atributos

Nesta seção, você irá configurar o fluxo de dados de usuário do Workday para o Active Directory.

1. Na guia Provisionamento, em **Mapeamentos**, clique em **Sincronizar trabalhadores do Workday com o Active Directory local**.

1. No campo **Escopo do objeto de origem** é possível selecionar quais conjuntos de usuários no Workday devem estar no escopo de provisionamento para AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é “todos os usuários no Workday”. Filtros de exemplo:

   * Exemplo: O escopo para usuários com IDs do Trabalhador entre 1000000 e 2000000 (excluindo 2000000)

      * Atributo: WorkerID

      * Operador: Coincidir EXREG

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas funcionários e trabalhadores não contingentes

      * Atributo: EmployeeID

      * Operador: IS NOT NULL

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento de aplicativo pela primeira vez, você precisará testar e verificar seus mapeamentos de atributo e expressões para certificar-se de que ele está dando a você o resultado desejado. A Microsoft recomenda usar os [filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) sob **Escopo do Objeto de Origem** e [provisionamento sob demanda](../app-provisioning/provision-on-demand.md) para testar seus mapeamentos com alguns usuários de teste do Workday. Após ter verificado que os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários fora do escopo. Isso pode não ser desejável em sua integração do Workday com o AD. Para substituir esse comportamento padrão, consulte o artigo [Ignorar a exclusão de contas de usuário fora do escopo](../app-provisioning/skip-out-of-scope-deletions.md)

1. No campo **Ações do objeto de destino** é possível filtrar globalmente quais ações são executadas no Active Directory. **Criar** e **Atualizar** são as mais comuns.

1. Na seção **Mapeamentos de atributos** é possível definir como os atributos individuais do Workday mapeiam atributos do Active Directory.

1. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual dá suporte para essas propriedades:

      * **Tipo de Mapeamento**

         * **Direto** – grava o valor do atributo do Workday no atributo AD, sem alterações

         * **Constante** - grava um valor de cadeia de caracteres constante estático para o atributo do AD

         * **Expressão** – Permite gravar um valor personalizado para o atributo do AD baseado em um ou mais atributos do Workday. [Para obter mais informações, consulte este artigo sobre expressões](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atributo de origem** - O atributo de usuário do Workday. Se o atributo que você está procurando não estiver presente, consulte [Personalizar a lista de atributos de usuário do Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valor padrão** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento irá gravar esse valor.
            A configuração mais comum é deixar em branco.

      * **Atributo de destino** –  o atributo de usuário no Active Directory.

      * **Corresponder objetos utilizando esse atributo** – Se esse mapeamento deverá ou não ser utilizado para identificar usuários do Workday e Active Directory com exclusividade. Normalmente, esse valor é definido no campo ID do Trabalhador para o Workday que geralmente é mapeado para um dos atributos do ID do Funcionário no Active Directory.

      * **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum outro atributo correspondente será avaliado.

      * **Aplicar esse mapeamento**

         * **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário

         * **Somente durante a criação** – aplicar esse mapeamento somente nas ações de criação de usuário

1. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.
   >[!div class="mx-imgBorder"]
   >![Captura de tela que mostra a página "Mapeamento de Atributos" com a ação "Salvar" selecionada.](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>A seguir, estão alguns exemplos de mapeamentos de atributos entre o Workday e o Active Directory, com algumas expressões comuns

* A expressão que mapeia para o atributo *parentDistinguishedName* é utilizada para provisionar um usuário para OUs diferentes baseado em um ou mais atributos de origem do Workday. Este exemplo coloca os usuários em OUs diferentes com base na cidade que eles estão.

* O atributo *userPrincipalName* no Active Directory é gerado usando a função de eliminação de duplicação [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) que verifica a existência de um valor gerado no domínio do AD de destino e apenas define se ele é exclusivo.  

* [Há documentação sobre expressões de gravação aqui](../app-provisioning/functions-for-customizing-application-data.md). Esta seção inclui exemplos sobre como remover caracteres especiais.

| ATRIBUTO WORKDAY | ATRIBUTO DO ACTIVE DIRECTORY |  ID DE CORRESPONDÊNCIA? | CRIAR / ATUALIZAR |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Sim** | Gravado na criação somente |
| **PreferredNameData**    |  cn    |   |   Gravado na criação somente |
| **SelectUniqueValue( Join("\@", Join(".",  \[FirstName\], \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Gravado na criação somente 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Gravado na criação somente |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Criar + atualizar |
| **Nome**   | givenName       |     |    Criar + atualizar |
| **Sobrenome**   |   sn   |     |  Criar + atualizar |
| **PreferredNameData**  |  displayName |     |   Criar + atualizar |
| **Empresa**         | company   |     |  Criar + atualizar |
| **SupervisoryOrganization**  | department  |     |  Criar + atualizar |
| **ManagerReference**   | manager  |     |  Criar + atualizar |
| **BusinessTitle**   |  título     |     |  Criar + atualizar | 
| **AddressLineData**    |  streetAddress  |     |   Criar + atualizar |
| **Município**   |   l   |     | Criar + atualizar |
| **CountryReferenceTwoLetter**      |   co |     |   Criar + atualizar |
| **CountryReferenceTwoLetter**    |  c  |     |         Criar + atualizar |
| **CountryRegionReference** |  st     |     | Criar + atualizar |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Criar + atualizar |
| **PostalCode**  |   postalCode  |     | Criar + atualizar |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Criar + atualizar |
| **Fax**      | facsimileTelephoneNumber     |     |    Criar + atualizar |
| **Móvel**  |    Serviço Móvel       |     |       Criar + atualizar |
| **LocalReference** |  preferredLanguage  |     |  Criar + atualizar |                                               
| **Switch(\[Municipality\], "OU=Default Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Criar + atualizar |

Depois que a configuração de mapeamento de atributos for concluída, você poderá testar o provisionamento para um único usuário usando [provisionamento sob demanda](../app-provisioning/provision-on-demand.md) e, em seguida, [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do Workday tiverem sido concluídas e você tiver verificado o provisionamento para um usuário individual com o [provisionamento sob demanda](../app-provisioning/provision-on-demand.md), você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros em problemas de dados de mapeamento ou Workday, o trabalho de provisionamento pode falhar e prosseguir para o estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste usando o [provisionamento sob demanda](../app-provisioning/provision-on-demand.md) antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Acesse a folha **Provisionamento** e clique em **Iniciar o provisionamento**.

1. Essa operação dará início à sincronização inicial, o que poderá demorar algumas horas dependendo de quantos usuários estiverem no locatário do Workday. Verifique a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

1. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização realizados pelo serviço de provisionamento, tais como aqueles em que os usuários estão sendo lidos do Workday e, posteriormente adicionados ou atualizados no Active Directory. Consulte a seção Solução de problemas para obter instruções sobre como examinar os logs de auditoria e corrigir erros de provisionamento.

1. Após a sincronização inicial ser concluída, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.
   > [!div class="mx-imgBorder"]
   > ![Barra de progresso do provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

* **Perguntas do recurso de solução**
  * [Ao processar uma nova contratação do Workday, como a solução define a senha para a nova conta de usuário no Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [A solução dá suporte a envio de notificações por email após a conclusão de operações de provisionamento?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [A solução armazena em cache os perfis de usuário do Workday na nuvem do Azure AD ou na camada de agente de provisionamento?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [A atribuição dá suporte a atribuição de grupos locais do AD para o usuário?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Quais APIs do Workday a solução utiliza para consultar e atualizar perfis de trabalhadores do Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Posso configurar meu locatário do HCM do Workday com dois locatários do Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Como sugerir melhorias ou solicitar novos recursos relacionados à integração do Workday e do Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Perguntas sobre o Agente de Provisionamento**
  * [O que é a versão de disponibilidade geral (GA) do Agente de Provisionamento?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Como saber a versão do meu Agente de Provisionamento?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [A Microsoft envia por push automaticamente as atualizações do Agente de Provisionamento?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Posso instalar o Agente de Provisionamento no mesmo servidor que executa o Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Como fazer para configurar o Agente de Provisionamento para usar um servidor proxy para comunicação HTTP de saída?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Como garantir que o Agente de Provisionamento é capaz de se comunicar com o locatário do Azure AD e que nenhum firewall está bloqueando as portas exigidas pelo agente?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Como cancelar o registro do domínio associado ao meu Agente de Provisionamento?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Como desinstalar o Agente de Provisionamento?](#how-do-i-uninstall-the-provisioning-agent)

* **Perguntas sobre configuração e mapeamento de atributos do Workday para o AD**
  * [Como fazer backup ou exportar uma cópia funcional do meu esquema e mapeamento de atributos de provisionamento do Workday?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Eu tenho atributos personalizados no Workday e no Active Directory. Como fazer para configurar a solução para trabalhar com os meus atributos personalizados?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Posso configurar a foto do usuário do Workday para o Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Como faço para sincronizar os números de celular do Workday com base no consentimento do usuário para uso público?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Como fazer para formatar nomes de exibição no AD com base nos atributos de departamento/país/cidade do usuário e manipular as variações regionais?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Como usar SelectUniqueValue para gerar valores exclusivos para o atributo samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Como remover caracteres com diacríticos e convertê-los em letras normais do alfabeto em português?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Perguntas do recurso de solução

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Ao processar uma nova contratação do Workday, como a solução define a senha para a nova conta de usuário no Active Directory?

Quando o agente de provisionamento local obtém uma solicitação para criar uma nova conta do AD, ele automaticamente gera uma senha aleatória complexa, projetada para atender aos requisitos de complexidade de senha definidos pelo servidor do AD e define isso no objeto do usuário. Essa senha não é registrada em nenhum lugar.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>A solução dá suporte a envio de notificações por email após a conclusão de operações de provisionamento?

Não, enviar notificações por email após a conclusão de operações de provisionamento não tem suporte na versão atual.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>A solução armazena em cache os perfis de usuário do Workday na nuvem do Azure AD ou na camada de agente de provisionamento?

Não, a solução não mantém um cache de perfis de usuário. O serviço de provisionamento do Azure AD simplesmente atua como um processador de dados, lendo dados do Workday e gravando Active Directory ou Azure AD de destino. Consulte a seção [Gerenciamento de dados pessoais](#managing-personal-data) para obter detalhes relacionados à retenção de dados e privacidade do usuário.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>A atribuição dá suporte a atribuição de grupos locais do AD para o usuário?

Não há suporte para essa funcionalidade no momento. A solução alternativa recomendada é implantar um script do PowerShell que consulta o ponto de extremidade da API do Graph para [dados de log de auditoria](/graph/api/resources/azure-ad-auditlog-overview) e usá-lo para disparar cenários, como atribuição de grupo. Este script do PowerShell pode ser anexado a um agendador de tarefas e pode ser implantado na mesma caixa que executa o agente de provisionamento.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Quais APIs do Workday a solução utiliza para consultar e atualizar perfis de trabalhadores do Workday?

Atualmente, a solução usa as seguintes APIs do Workday:

* O formato da **URL da API dos Serviços Web do Workday** usado na seção **Credenciais de Administrador** determina a versão da API usada para Get_Workers
  * Se o formato da URL for https://\#\#\#\#\.workday\.com/ccx/service/tenantName, será usada a API v21.1. 
  * Se o formato da URL for https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources, será usada a API v21.1 
  * Se o formato da URL for https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources/v\#\#\.\#, será usada a versão da API especificada. (Exemplo: se a v34.0 for especificada, esta será a versão usada.)  

* O recurso de write-back de email do Workday usa Change_Work_Contact_Information (v30.0) 
* O recurso de write-back de nome de usuário do Workday usa Update_Workday_Account (v31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Posso configurar meu locatário do HCM do Workday com dois locatários do Azure AD?

Sim, há suporte para essa configuração. Aqui estão as etapas de alto nível para configurar esse cenário:

* Implantar o agente de provisionamento 1 e registrá-lo no locatário 1 do Azure AD.
* Implantar o agente de provisionamento 2 e registrá-lo no locatário 2 do Azure AD.
* Com base nos “Domínios filhos” que cada Agente de Provisionamento irá gerenciar, configure cada agente com o(s) domínio(s). Um agente pode lidar com vários domínios.
* No portal do Azure, configure o aplicativo de Provisionamento de Usuário do Workday para o AD em cada locatário e configure-o com os respectivos domínios.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Como sugerir melhorias ou solicitar novos recursos relacionados à integração do Workday e do Azure AD?

Seus comentários são muito importantes, pois eles nos ajudam a definir um direcionamento para as futuras versões e aprimoramentos. Estamos abertos a todos os comentários e encorajamos você a enviar sua ideia ou sugestão de melhoria na [fórum de comentários do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Para comentários específicos relacionados à integração do Workday, selecione a categoria *Aplicativos SaaS* e pesquise usando as palavras-chave *Workday* para encontrar os comentários existentes relacionados ao Workday.

> [!div class="mx-imgBorder"]
> ![Aplicativos SaaS do UserVoice](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![Workday do UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Ao sugerir uma nova ideia, verifique para ver se alguém já sugeriu um recurso semelhante. Nesse caso, você pode votar na solicitação de recurso ou aprimoramento. Você também pode deixar um comentário sobre o seu caso de uso específico para mostrar seu apoio à ideia e demonstrar como o recurso será importante para você também.

### <a name="provisioning-agent-questions"></a>Perguntas sobre o Agente de Provisionamento

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>O que é a versão de disponibilidade geral (GA) do Agente de Provisionamento?

Veja [Agente de Provisionamento do Azure AD Connect: histórico de lançamento de versões](../app-provisioning/provisioning-agent-release-version-history.md) para obter a última versão GA do Agente de Provisionamento.  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Como saber a versão do meu Agente de Provisionamento?

* Conecte-se ao servidor Windows no qual o Agente de Provisionamento está instalado.
* Vá até o menu **Painel de controle** -> **Desinstalar ou alterar um programa**
* Procure a versão correspondente à entrada **Agente de provisionamento do Microsoft Azure AD Connect**

  >[!div class="mx-imgBorder"]
  >![Azure portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>A Microsoft envia por push automaticamente as atualizações do Agente de Provisionamento?

Sim, a Microsoft atualiza automaticamente o agente de provisionamento se o serviço Windows **Atualizador do Agente do Microsoft Azure AD Connect** está em execução.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Posso instalar o Agente de Provisionamento no mesmo servidor que executa o Azure AD Connect?

Sim, você pode instalar o Agente de Provisionamento no mesmo servidor que executa o Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>No momento da configuração o Agente de Provisionamento solicita as credenciais de administrador do Azure AD. O Agente armazena as credenciais localmente no servidor?

Durante a configuração o Agente de Provisionamento solicita as credenciais de administrador do Azure AD somente para conectar o seu locatário do Azure AD. Ele não armazena as credenciais localmente no servidor. No entanto, ele retém as credenciais usadas para conectar-se ao *domínio local do Active Directory* em um cofre de senha local do Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Como fazer para configurar o Agente de Provisionamento para usar um servidor proxy para comunicação HTTP de saída?

O Agente de Provisionamento dá suporte ao uso do proxy de saída. Você pode configurá-lo editando o arquivo de configuração do agente **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Adicione as seguintes linhas a ele, no final do arquivo pouco antes da marca `</configuration>` de fechamento.
Substitua as variáveis [proxy-server] e [proxy-port] pelo nome do servidor proxy e pelos valores da porta.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Como garantir que o Agente de Provisionamento é capaz de se comunicar com o locatário do Azure AD e que nenhum firewall está bloqueando as portas exigidas pelo agente?

Verifique também se todas as [portas necessárias](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) estão abertas.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Um Agente de Provisionamento pode ser configurado para provisionar vários domínios do AD?

Sim, um Agente de Provisionamento pode ser configurado para lidar com vários domínios do AD, desde que o agente tenha metas claras para os respectivos controladores de domínio. A Microsoft recomenda configurar um grupo de 3 agentes de provisionamento servindo o mesmo conjunto de domínios do AD para garantir alta disponibilidade e fornecer suporte para failover.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Como cancelar o registro do domínio associado ao meu Agente de Provisionamento?

* No portal do Azure, obtenha a *ID do locatário* do seu locatário do Azure AD.
* Conecte-se ao servidor Windows no qual o Agente de Provisionamento está em execução.
* Abra o PowerShell como Administrador do Windows.
* Altere o diretório que contém os scripts de registro e execute os comandos a seguir substituindo o parâmetro de \[ID do locatário\] pelo valor da ID do locatário.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Na lista de agentes que aparecer, copie o valor do `id` campo cujo recurso *resourceName* seja igual ao nome de domínio do AD.
* Cole o valor de ID nesse comando e execute o comando no PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Execute novamente o assistente de configuração do Agente.
* Quaisquer outros agentes que foram atribuídos anteriormente a esse domínio precisarão ser reconfigurados.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Como desinstalar o Agente de Provisionamento?

* Conecte-se ao servidor Windows no qual o Agente de Provisionamento está instalado.
* Vá até o menu **Painel de controle** -> **Desinstalar ou alterar um programa**
* Desinstale os programas a seguir:
  * Agente de Provisionamento do Microsoft Azure AD Connect
  * Atualizador do Agente do Microsoft Azure AD Connect
  * Pacote do Agente de Provisionamento do Microsoft Azure AD Connect

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Perguntas sobre configuração e mapeamento de atributos do Workday para o AD

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Como fazer backup ou exportar uma cópia funcional do meu esquema e mapeamento de atributos de provisionamento do Workday?

Você pode usar a API do Microsoft Graph para exportar sua configuração de Provisionamento de Usuário do Workday. Consulte as etapas na seção [Exportar e importar a configuração de mapeamento de atributos de Provisionamento de Usuário do Workday](#exporting-and-importing-your-configuration) para obter detalhes.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Eu tenho atributos personalizados no Workday e no Active Directory. Como fazer para configurar a solução para trabalhar com os meus atributos personalizados?

A solução dá suporte a atributos personalizados do Workday e Active Directory. Para adicionar seus atributos personalizados ao esquema de mapeamento, abra a folha **Mapeamento de atributos** e role para baixo para expandir a seção **Mostrar opções avançadas**. 

![Editar a Lista de Atributos](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Para adicionar seus atributos personalizados do Workday, selecione a opção *Editar a lista de atributos para Workday* e para adicionar seus atributos personalizados do AD, selecione a opção *Editar lista de atributos no Active Directory local*.

Consulte também:

* [Personalizando a lista de atributos de usuário do Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Como fazer para configurar a solução para atualizar somente os atributos no AD com base nas alterações do Workday e não criar novas contas do AD?

Essa configuração pode ser feita definindo as **Ações do objeto de destino** na folha **Mapeamentos de atributos** conforme mostrado abaixo:

![Ação de atualizar](./media/workday-inbound-tutorial/wd_target_update_only.png)

Marque a caixa de seleção “Atualizar” apenas para operações de atualização para o fluxo do Workday para o AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Posso configurar a foto do usuário do Workday para o Active Directory?

A solução atualmente não dá suporte a atributos binários de configuração, como *thumbnailPhoto* e *jpegPhoto* no Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Como faço para sincronizar os números de celular do Workday com base no consentimento do usuário para uso público?

* Acesse a folha “Provisionamento” do Aplicativo de Provisionamento do Workday.
* Clique em Mapeamentos de atributos 
* Em **Mapeamentos**, selecione **Sincronizar trabalhadores do Workday com o Active Directory local** (ou **Sincronizar trabalhadores do Workday com o Azure AD**).
* Na página de Mapeamentos de Atributos, role para baixo e marque a caixa “Mostrar opções avançadas”.  Clique em **Editar lista de atributos do Workday**
* Na folha que é aberta, localize o atributo “Celular” e clique na linha para que você possa editar o ![RGPD do Celular](./media/workday-inbound-tutorial/mobile_gdpr.png) da **Expressão da API**

* Substitua a **expressão da API** pela seguinte expressão nova, que recupera o número de celular de trabalho somente se o “Sinalizador de uso público” estiver definido como “True” no Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Salve a Lista de Atributos.
* Salve o Mapeamento de Atributos.
* Limpe o estado atual e reinicie a sincronização completa.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Como fazer para formatar nomes de exibição no AD com base em atributos de país/departamento/cidade do usuário e manipular as variações regionais?

É um requisito comum configurar o atributo *displayName* no AD para que ele também forneça informações sobre o departamento e o país/região do usuário. Por exemplo, se John Smith trabalha no Departamento de Marketing nos Estados Unidos, você poderá querer que o seu *displayName* apareça como *Smith, John (Marketing-EUA)* .

Aqui está como você pode lidar com tais requisitos para construir *CN* ou *displayName* para incluir atributos como empresa, unidade de negócio, cidade ou país/região.

* Cada atributo do Workday é recuperado usando uma expressão de API XPATH subjacente, que pode ser configurada em **Mapeamento de Atributos -> Seção Avançada -> Editar lista de atributos para Workday**. Aqui está a expressão de API padrão do XPATH para os atributos *PreferredFirstName*, *PreferredLastName*, *empresa* e *SupervisoryOrganization* do Workday.

     | Atributo do Workday | Expressão XPATH da API |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Empresa | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |

   Confirme com sua equipe do Workday se a expressão de API acima é válida para a sua configuração de locatário do Workday. Se necessário, você pode editar conforme descrito na seção [Personalizar a lista de atributos de usuário do Workday](#customizing-the-list-of-workday-user-attributes).

* Da mesma forma, as informações de país/região presentes no Workday são recuperadas usando o XPATH a seguir: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Há 5 atributos relacionados ao país/região que estão disponíveis na seção de lista de atributos do Workday.

     | Atributo do Workday | Expressão XPATH da API |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Confirme com sua equipe do Workday se as expressões de API acima são válidas para a sua configuração de locatário do Workday. Se necessário, você pode editar conforme descrito na seção [Personalizar a lista de atributos de usuário do Workday](#customizing-the-list-of-workday-user-attributes).

* Para criar a expressão de mapeamento de atributos correta, identifique qual atributo do Workday representa “com autoridade” o primeiro nome, sobrenome, país/região e departamento do usuário. Digamos que os atributos sejam *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* e *SupervisoryOrganization* respectivamente. Você pode usar isso para criar uma expressão para o atributo *displayName* conforme a seguir para obter um nome de exibição, como *Smith, John (Marketing-EUA)* .

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Após conseguir a expressão correta, edite a tabela de Mapeamentos de Atributos e modifique o mapeamento do atributo *displayName*, conforme mostrado abaixo:   ![Mapeamento de DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Estendendo o exemplo acima, vamos dizer que você gostaria de converter nomes de cidades provenientes do Workday em valores de abreviação e, em seguida, usá-los para criar nomes de exibição, como *Smith, John (Nova York)* ou *Ninguém, Zé (NYC)* , em seguida, esse resultado pode ser obtido usando uma expressão Alternar com o atributo *Município* do Workday como a variável determinante.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Consulte também:
  * [Sintaxe da função Switch](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Sintaxe da função Join](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Sintaxe da função Append](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Como usar SelectUniqueValue para gerar valores exclusivos para o atributo samAccountName?

Digamos que você deseja gerar valores exclusivos para o atributo *samAccountName* usando uma combinação de atributos *FirstName* e *LastName* do Workday. A seguir está uma expressão com a qual você pode começar:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Como funciona a expressão acima: Se o usuário for John Smith, ela primeiro tenta gerar JSmith, se já existir JSmith, em seguida, ela gera JoSmith, se também já existir, ela gera JohSmith. A expressão também garante que o valor gerado atende à restrição de comprimento e à restrição de caracteres especiais associados a *samAccountName*.

Consulte também:

* [Sintaxe da função Mid](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Sintaxe da função Replace](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Sintaxe da função SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Como remover caracteres com diacríticos e convertê-los em letras normais do alfabeto em português?

Use a função [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) para remover caracteres especiais no nome e sobrenome do usuário, ao construir o valor do endereço de email ou CN para o usuário.

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

Esta seção fornece diretrizes específicas sobre como solucionar problemas de provisionamento com a integração do Workday usando os logs de auditoria do Azure AD e os logs do Visualizador de eventos do Windows Server. Ela complementa as etapas de solução de problemas genéricas e os conceitos mostrados no [Tutorial: Relatórios sobre o provisionamento automático de conta de usuário](../app-provisioning/check-status-user-account-provisioning.md)

Esta seção cobre os seguintes aspectos de solução de problemas:

* [Configurar o agente de provisionamento para emitir logs do Visualizador de Eventos](#configure-provisioning-agent-to-emit-event-viewer-logs)
* [Configurar o Visualizador de Eventos do Windows para solucionar problemas do agente](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Configurar os logs de auditoria do portal do Azure para solucionar problemas de serviço](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Entender logs para operações de criação da conta de usuário do AD](#understanding-logs-for-ad-user-account-create-operations)
* [Entender logs para operações de atualização do gerente](#understanding-logs-for-manager-update-operations)
* [Resolver erros comumente encontrados](#resolving-commonly-encountered-errors)

### <a name="configure-provisioning-agent-to-emit-event-viewer-logs"></a>Configurar o agente de provisionamento para emitir logs do Visualizador de Eventos
1. Conecte-se ao computador do Windows Server no qual o agente de provisionamento está implantado
1. Interrompa o **Agente de Provisionamento do Microsoft Azure AD Connect**.
1. Crie uma cópia do arquivo de configuração original: *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Substitua a seção `<system.diagnostics>` existente pelo mostrado a seguir. 
   * A configuração **etw** do ouvinte emite mensagens para os logs do Visualizador de Eventos
   * A configuração **textWriterListener** do ouvinte envia mensagens de rastreamento para o arquivo *ProvAgentTrace.log*. Remova as marcas de comentário das linhas relacionadas a textWriterListener apenas para solução de problemas avançada. 

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <!-- <add name="textWriterListener"/> -->
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <!-- <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/> -->
         </sharedListeners>
     </system.diagnostics>

   ```
1. Inicie o serviço do **Agente de Provisionamento do Microsoft Azure AD Connect**.

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Configurar o Visualizador de Eventos do Windows para solucionar problemas do agente

1. Conecte-se ao Windows Server no qual o Agente de Provisionamento está implantado
1. Abra o aplicativo da área de trabalho **Visualizador de Eventos do Windows Server**.
1. Selecione **Logs do Windows > Aplicativo**.
1. Use a opção **Filtrar log atual...** para exibir todos os eventos registrados na fonte **Agente de Provisionamento do Azure AD Connect** e exclua eventos com a ID de evento "5", especificando o filtro "-5", conforme mostrado abaixo.
   > [!NOTE]
   > A ID de Evento 5 captura mensagens de bootstrap do agente para o serviço de nuvem do Azure AD e, portanto, nós a filtramos durante a análise dos arquivos de log. 

   ![Visualizador de Eventos do Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png)

1. Clique em **OK** e classifique a exibição dos resultados por colunas de **Data e Hora**.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Configurar os logs de auditoria do portal do Azure para solucionar problemas de serviço

1. Inicie o [Portal do Azure](https://portal.azure.com) e navegue até a seção **Logs de auditoria** do aplicativo de provisionamento do Workday.
1. Use o botão **Colunas** na página de Logs de auditoria para exibir apenas as colunas a seguir no modo de exibição (Data, Atividade, Status, Motivo do Status). Essa configuração garante que você se concentre apenas nos dados que são relevantes para a solução de problemas.

   ![Colunas do log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_00.png)

1. Use os parâmetros de consulta **Destino** e **Intervalo de datas** para filtrar a exibição. 
   * Defina o parâmetro de consulta **Destino** para o “ID de Trabalho” ou “ID do Funcionário” do objeto de trabalho do Workday.
   * Defina o **Intervalo de datas** como um período de tempo apropriado ao longo do qual você deseja investigar erros ou problemas com o provisionamento.

   ![Filtros de log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Entender logs para operações de criação da conta de usuário do AD

Quando uma nova contratação no Workday é detectada (digamos com ID de Funcionário *21023*), o serviço de provisionamento do Azure AD tenta criar uma nova conta de usuário do AD para o trabalhador e, durante esse processo, cria 4 registros de log de auditoria conforme descrito abaixo:

  [![Operação de criação de log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Quando você clica em qualquer um dos registros de log de auditoria, a página **Detalhes da atividade** abre. Aqui está o que a página **Detalhes da atividade** exibe para cada tipo de registro de log.

* Registro de **Importação do Workday**: Este registro de log exibe as informações do trabalhador obtidas no Workday. Utilize as informações na seção *Detalhes adicionais* do registro de log para solucionar problemas com a busca de dados no Workday. Um registro de exemplo é mostrado abaixo, junto com os ponteiros para interpretar cada campo.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Registro de **Importação do AD**: Este registro de log exibe informações da conta buscadas no AD. Como não há nenhuma conta de AD durante a criação inicial do usuário o *Motivo do status de atividade* indicará que nenhuma conta com o valor do atributo de ID de Correspondência foi encontrada no Active Directory. Utilize as informações na seção *Detalhes adicionais* do registro de log para solucionar problemas com a busca de dados no Workday. Um registro de exemplo é mostrado abaixo, junto com os ponteiros para interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Para localizar registros de log do Agente de Provisionamento correspondentes a essa operação de importação do AD, abra os logs do Visualizador de Eventos do Windows e use a opção **Localizar...** do menu para localizar entradas de log que contém o valor do atributo de ID de correspondência/Propriedade Joining (neste caso, *21023*).

  ![Localizar](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Procure a entrada com a *ID de Evento = 9*, que fornecerá a você o filtro de pesquisa LDAP usado pelo agente para recuperar a conta do AD. Você pode verificar se esse é o filtro de pesquisa correto para recuperar entradas de usuário exclusivas.

  ![Pesquisa LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  O registro que vem imediatamente a seguir com *ID de Evento = 2* captura o resultado da operação de pesquisa e se ela retornou algum resultado.

  ![Resultados de LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Registro de **ação de regra de sincronização**: Este registro de log exibe os resultados das regras de mapeamento de atributo e dos filtros de escopo configurados junto com a ação de provisionamento que serão usados para processar o evento de entrada do Workday. Utilize as informações na seção *Detalhes adicionais* do registro de log para solucionar problemas com a ação de sincronização. Um registro de exemplo é mostrado abaixo, junto com os ponteiros para interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Se houver problemas com as expressões de mapeamento de atributos ou os dados de entrada do Workday apresentarem problemas (por exemplo: valor vazio ou nulo para os atributos necessários), você observará uma falha nesta fase com o código de erro fornecendo os detalhes da falha.

* Registro de **Exportação do AD**: Este registro de log exibe o resultado da operação de criação de conta do AD junto com os valores de atributo que foram definidas no processo. Utilize as informações na seção *Detalhes adicionais* do registro de log para solucionar problemas com a ação de criação de conta. Um registro de exemplo é mostrado abaixo, junto com os ponteiros para interpretar cada campo. Na seção “Detalhes adicionais”, “EventName” é definido como “EntryExportAdd”, “JoiningProperty” é definido como o valor do atributo de ID de Correspondência, o “SourceAnchor” é definido como o WorkdayID (WID) associado ao registro e “TargetAnchor” é definido como o valor do atributo “ObjectGuid” do AD do usuário recém-criado. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Para localizar registros de log do Agente de Provisionamento correspondentes a essa operação de exportação do AD, abra os logs do Visualizador de Eventos do Windows e use a opção **Localizar...** do menu para localizar entradas de log que contém o valor do atributo de ID de correspondência/Propriedade Joining (neste caso, *21023*).  

  Procure um registro de HTTP POST correspondente ao carimbo de data/hora da operação de exportação com *ID de Evento = 2*. Esse registro contém os valores de atributo enviados pelo serviço de provisionamento para o agente de provisionamento.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_05.png" alt-text="Captura de tela que mostra o registro 'HTTP POST' no log do 'Agente de Provisionamento'." lightbox="media/workday-inbound-tutorial/wd_event_viewer_05.png":::

  Imediatamente após o evento acima, deve haver outro evento que captura a resposta da operação de criação de conta do AD. Esse evento retorna o novo objectGuid criado no AD e é definido como o atributo TargetAnchor no serviço de provisionamento.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_06.png" alt-text="Captura de tela que mostra o log do 'Agente de Provisionamento' com o objectGuid criado no AD realçado." lightbox="media/workday-inbound-tutorial/wd_event_viewer_06.png":::

### <a name="understanding-logs-for-manager-update-operations"></a>Entender logs para operações de atualização do gerente

O atributo gerente é um atributo de referência no AD. O serviço de provisionamento não define o atributo gerente como parte da operação de criação de usuário. Em vez disso, o atributo gerente é definido como parte de uma operação *atualizar* depois que a conta do AD é criada para o usuário. Expandindo o exemplo acima, digamos que uma nova contratação com ID de Funcionário “21451” é ativada no Workday e o gerente da nova contratação (*21023*) já tem uma conta do AD. Nesse cenário, pesquisar os Logs de auditoria para o usuário 21451 mostra 5 entradas.

  [![Atualização do Gerente](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Os 4 primeiros registros são aqueles que exploramos como parte da operação de criação do usuário. O quinto registro é a exportação associada à atualização do atributo de gerente. O registro de log exibe o resultado da operação de atualização do gerente de conta do AD, que é executada usando o atributo *objectGuid* do gerente.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Resolver erros comumente encontrados

Esta seção aborda os erros comuns com o provisionamento de usuário do Workday e como resolvê-los. Os erros são agrupados conforme indicado a seguir:

* [Erros do agente de provisionamento](#provisioning-agent-errors)
* [Erros de conectividade](#connectivity-errors)
* [Erros de criação de conta de usuário do AD](#ad-user-account-creation-errors)
* [Erros de atualização de conta de usuário do AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Erros do agente de provisionamento

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Erro ao instalar o agente de provisionamento com a mensagem de erro:  *Falha ao iniciar o serviço “Agente de Provisionamento do Microsoft Azure AD Connect” (AADConnectProvisioningAgent). Verifique se você tem privilégios suficientes para iniciar o sistema.* | Esse erro normalmente aparece se você está tentando instalar o agente de provisionamento em um controlador de domínio e a política de grupo impede que o serviço seja iniciado.  Ele também é visto se você tiver uma versão anterior do agente em execução e não o tiver desinstalado antes de iniciar uma nova instalação.| Instale o agente de provisionamento em um servidor que não seja um controlador de domínio. Certifique-se de que as versões anteriores do agente são desinstaladas antes de instalar o novo agente.|
|2.| O serviço do Windows “Agente de Provisionamento do Microsoft Azure AD Connect” está no estado *Iniciando* e não muda para o estado *Em execução*. | Como parte da instalação, o assistente de agente cria uma conta local (**NT Service\\AADConnectProvisioningAgent**) no servidor e essa é a conta de logon usada para iniciar o serviço. Se uma política de segurança em seu servidor Windows impede as contas locais de executarem os serviços, você encontrará esse erro. | Abra o *console de Serviços*. Clique com o botão direito no serviço do Windows “Agente de Provisionamento do Microsoft Azure AD Connect” e, na guia Logon, especifique a conta de administrador de domínio para executar o serviço. Reinicie o serviço. |
|3.| Ao configurar o agente de provisionamento com seu domínio do AD na etapa *Conectar o Active Directory*, o assistente leva muito tempo tentando carregar o esquema do AD e eventualmente expira. | Esse erro normalmente aparece se o assistente não puder entrar em contato com o servidor de controlador de domínio do AD devido a problemas de firewall. | Na tela do assistente *Conectar o Active Directory*, além de fornecer as credenciais para o seu domínio do AD, há uma opção chamada *Selecionar prioridade do controlador de domínio*. Use esta opção para selecionar um controlador de domínio que esteja no mesmo site que o servidor do agente e certifique-se de que não há nenhuma regra de firewall bloqueando a comunicação. |

#### <a name="connectivity-errors"></a>Erros de conectividade

Se o serviço de provisionamento não conseguir se conectar ao Workday ou ao Active Directory, isso pode fazer com que o provisionamento entre em um estado de quarentena. Use a tabela abaixo para solucionar problemas de conectividade.

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Ao clicar em **Testar conexão** você receberá a mensagem de erro: *Ocorreu um erro ao conectar-se ao Active Directory. Verifique se o Agente de Provisionamento local está sendo executado e está configurado com o domínio correto do Active Directory.* | Esse erro geralmente mostra se o agente de provisionamento não está em execução ou se há um firewall bloqueando a comunicação entre o Azure AD e o agente de provisionamento. Você também poderá ver esse erro se o domínio não estiver configurado no assistente do agente. | Abra o console *Serviços* console no servidor do Windows para confirmar se o agente está em execução. Abra o assistente do agente de provisionamento e confirme se o domínio correto está registrado com o agente.  |
|2.| O trabalho de provisionamento entrará em um estado de quarentena nos finais de semana (Sex - Sáb) e receberemos uma notificação por email que há um erro com a sincronização. | Uma das causas comuns desse erro é o tempo de inatividade planejado do Workday. Se você estiver usando um locatário de implementação do Workday, observe que o Workday tem tempo de inatividade programado para seus locatários de implementação nos finais de semana (geralmente da noite da sexta-feira até a manhã de sábado) e, durante esse período, os aplicativos de provisionamento do Workday podem entrar em um estado de quarentena, pois eles não conseguem conectar-se ao Workday. Eles voltam ao estado normal quando o locatário de implementação do Workday estiver online novamente. Em casos raros, você também poderá ver esse erro, se a senha do usuário do sistema de integração foi alterada devido à atualização do locatário ou se a conta estiver no estado bloqueado ou expirado. | Verifique com seu parceiro de integração ou administrador do Workday para ver quando o agendamento do tempo de inatividade do Workday para ignorar mensagens de alerta durante o período de tempo de inatividade e confirmar a disponibilidade quando a instância do Workday estiver online novamente.  |

#### <a name="ad-user-account-creation-errors"></a>Erros de criação de conta de usuário do AD

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Exportar falhas em operações no log de auditoria com a mensagem *Erro: OperationsError-SvcErr: Erro de operação. Nenhuma referência superior foi configurada para o serviço de diretório. Portanto, o serviço de diretório não pode emitir referências a objetos fora desta floresta.* | Esse erro geralmente mostra se o *contêiner do Active Directory* UO não está definido corretamente ou se há problemas com o Mapeamento da Expressão usado para *parentDistinguishedName*. | Verifique o parâmetro do *Contêiner do Active Directory* UO para erros de digitação. Se você estiver usando *parentDistinguishedName* no mapeamento de atributos certifique-se de que ele sempre é avaliado como um contêiner conhecido dentro do domínio do AD. Verifique o evento *Exportar* nos logs de auditoria para ver o valor gerado. |
|2.| Exportar falhas em operações no log de auditoria com o código de erro: *SystemForCrossDomainIdentityManagementBadResponse* e a mensagem *Erro: ConstraintViolation-AtrErr: O valor na solicitação é inválido. Um valor para o atributo não estava no intervalo de valores aceitável. Detalhes de \nError: CONSTRAINT_ATT_TYPE - company*. | Embora esse erro seja específico para o atributo *empresa*, você poderá ver esse erro também para outros atributos como *CN*, por exemplo. Este erro aparece devido à restrição de esquema do AD imposta. Por padrão, os atributos *empresa* e *CN* no AD têm um limite máximo de 64 caracteres. Se o valor proveniente do Workday tiver mais que 64 caracteres, então você verá essa mensagem de erro. | Verifique o evento *Exportar* nos logs de auditoria para ver o valor do atributo relatado na mensagem de erro. Considere a possibilidade de truncar o valor proveniente do Workday usando a função [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) ou alterar os mapeamentos para um atributo do AD que não tenha essas mesmas restrições de tamanho.  |

#### <a name="ad-user-account-update-errors"></a>Erros de atualização de conta de usuário do AD

Durante o processo de atualização de conta de usuário do AD, o serviço de provisionamento lê as informações do Workday e do AD, executa as regras de mapeamento de atributos e determina se é necessário realizar alguma alteração. Da mesma forma, um evento de atualização é acionado. Se ocorrer uma falha em qualquer uma dessas etapas, ela é registrada nos logs de auditoria. Use a tabela abaixo para solucionar problemas comuns com erros de atualização.

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Falhas de ação da regra de sincronização no log de auditoria com a mensagem *EventName = EntrySynchronizationError e ErrorCode = EndpointUnavailable*. | Esse erro aparecerá se o serviço de provisionamento não consegue recuperar os dados de perfil do usuário do Active Directory devido a um erro de processamento encontrado pelo agente de provisionamento local. | Verifique os logs do Visualizador de Eventos do Agente de Provisionamento para eventos de erro que indicam problemas com a operação de leitura (Filtrar por ID de Evento 2). |
|2.| O atributo de gerente no AD não será atualizado para determinados usuários no AD. | A causa mais provável desse erro é se você estiver usando regras de escopo e o gerente do usuário não é parte do escopo. Você também pode encontrar esse problema se o atributo de ID de correspondência do gerente (por exemplo, EmployeeID) não for encontrado no domínio do AD de destino ou não estiver definido com o valor correto. | Revise o filtro de escopo e adicione o usuário gerente no escopo. Verifique o perfil de gerente do AD para certificar-se de que há um valor para o atributo de ID de correspondência. |

## <a name="managing-your-configuration"></a>Gerenciar sua configuração

Esta seção descreve como você pode aproveitar, personalizar e gerenciar ainda mais sua configuração de provisionamento de usuário no Workday. Ela abrange os seguintes tópicos:

* [Personalizando a lista de atributos de usuário do Workday](#customizing-the-list-of-workday-user-attributes)  
* [Exportar e importar sua configuração](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizando a lista de atributos de usuário do Workday

O provisionamento de aplicativos do Workday para o Active Directory e o Azure AD incluem uma lista padrão de atributos de usuário do Workday da qual você pode selecionar. No entanto, essas listas não são abrangentes. O Workday é compatível com centenas de possibilidades de atributos de usuário, que podem ser padrão ou exclusivos para seu locatário do Workday.

O serviço de provisionamento do Azure AD é compatível com possibilidade de personalizar sua lista ou seu atributo do Workday a fim de incluir todos os atributos expostos na operação [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) da API de Recursos Humanos.

Para fazer essa alteração, você deve usar o [Workday Studio](https://community.workday.com/studio-download) para extrair as expressões XPath que representam os atributos que você deseja usar e, em seguida, adicioná-los à sua configuração de provisionamento usando o editor de atributo avançado no Portal do Azure.

**Para recuperar uma expressão XPath para um atributo de usuário do Workday:**

1. Baixe e instale o [Workday Studio](https://community.workday.com/studio-download). Você precisará de uma conta da comunidade do Workday para acessar o instalador.

2. Baixe o arquivo WSDL  **Human_Resources** do Workday específico para a versão da API do WWS que você planeja usar no [Diretório de serviços Web do Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)

3. Inicie o Workday Studio.

4. Na barra de comandos, selecione a opção **Workday > Testar o serviço Web no Testador**.

5. Selecione **Externo** e selecione o arquivo WSDL Human_Resources que você baixou na etapa 2.

    ![Captura de tela que mostra o arquivo "Human_Resources" aberto no Workday Studio.](./media/workday-inbound-tutorial/wdstudio1.png)

6. Defina o campo **local** como `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, mas substituindo "IMPL-CC" pelo tipo de instância real e "LOCATÁRIO" pelo seu nome do locatário real.

7. Defina **Operação** como **Get_Workers**

8.    Clique no pequeno link **configurar** abaixo dos painéis de Solicitação/Resposta para definir suas credenciais do Workday. Marque a opção **Autenticação** e, em seguida, insira o nome de usuário e senha da sua conta do sistema de integração do Workday. Não se esqueça de formatar o nome de usuário como nome\@locatário e deixar a opção **WS-Security UsernameToken** selecionada.
   ![Captura de tela que mostra a guia "Segurança" com o "Nome de usuário" e a "Senha" inseridos e o "Token de Nome de Usuário do WS-Security" selecionado.](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecione **OK**.

10. No painel **Solicitação**, cole o XML abaixo. Defina **Employee_ID** à ID de funcionário de um usuário real em seu locatário do Workday. Defina **wd:version** para a versão do WWS que você planeja usar. Selecione um usuário que tenha o atributo que você deseja extrair preenchido.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Clique em **Enviar Solicitação** (seta verde) para executar o comando. Se for bem-sucedido, a resposta deverá aparecer no painel **Resposta**. Verifique a resposta para garantir que ela tenha os dados da ID de usuário que você inseriu e não um erro.

12. Se for bem-sucedido, copie o XML do painel **Resposta** e salve-o como um arquivo XML.

13. Na barra de comandos do Workday Studio, selecione **File> Open File ...** e abra o arquivo XML que você salvou. Essa ação abrirá o arquivo no editor XML do Workday Studio.

    ![Captura de tela de um arquivo XML aberto no "editor de XML do Workday Studio".](./media/workday-inbound-tutorial/wdstudio3.png)

14. Na árvore de arquivo, navegue por **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Trabalhador** para localizar os dados do seu usuário.

15. Em **wd: Trabalhador**, localize o atributo que você quer adicionar e selecione-o.

16. Copie a expressão XPath para o atributo que você selecionou no campo **Caminho de Documento**.

17. Remova o prefixo **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** da expressão copiada.

18. Se o último item na expressão copiada for um nó (exemplo: "/wd: Birth_Date"), então acrescente **/text()** no final da expressão. Isso não é necessário se o último item for um atributo (exemplo: "/@wd: type").

19. O resultado deve ser algo como `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Esse valor é o que você copiará no Portal do Azure.

**Para adicionar o atributo de usuário personalizado do Workday à sua configuração de provisionamento:**

1. Inicie o [Portal do Azure](https://portal.azure.com) e navegue até a seção Provisionamento do aplicativo de provisionamento do Workday, conforme descrito anteriormente neste tutorial.

2. Defina o **Status de Provisionamento** como **Desativado** e selecione **Salvar**. Essa etapa ajudará a garantir que as alterações entrem em vigor somente quando você estiver pronto.

3. Em **Mapeamentos**, selecione **Sincronizar trabalhadores do Workday com o Active Directory local** (ou **Sincronizar trabalhadores do Workday com o Azure AD**).

4. Role até a parte inferior da próxima tela e selecione **Mostrar opções avançadas**.

5. Selecione **Editar lista de atributos do Workday**.

    ![Captura de tela que mostra a página "Provisionamento de Usuário do Workday para o Azure AD – Provisionamento" com a ação "Editar lista de atributos do Workday" realçada.](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Role até a parte inferior da lista de atributos, até o local em que estão os campos de entrada.

7. Em **Nome**, insira um nome de exibição para o atributo.

8. Em **Tipo**, selecione o tipo que corresponda adequadamente ao seu atributo (**Cadeia de Caracteres** é mais comum).

9. Em **Expressão API**, insira a expressão XPath que você copiou do Workday Studio. Exemplo: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selecione **Adicionar Atributo**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selecione **Salvar** acima e, em seguida, **Sim**, na caixa de diálogo. Feche a tela Mapeamento de Atributo, se ainda estiver aberta.

12. Na guia **Provisionamento** principal, selecione **Sincronizar trabalhos do Workday com o Active Directory local** (ou **Sincronizar trabalhos com o Azure AD**) novamente.

13. Selecione **Adicionar novo mapeamento**.

14. Agora, o novo atributo deve aparecer na lista **Atributo de origem**.

15. Adicione um mapeamento para o novo atributo conforme desejado.

16. Quando terminar, lembre-se de definir novamente o **Status de Provisionamento** como **Ativado** e salvar.

### <a name="exporting-and-importing-your-configuration"></a>Exportar e importar sua configuração

Consulte o artigo [Exportar e importar a configuração de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Gerenciando dados pessoais

A solução de provisionamento do Workday para o Active Directory exige que um agente de sincronização seja instalado em um servidor local do Windows, e esse agente cria logs no log de eventos do Windows podendo conter dados pessoais dependendo dos seus mapeamentos de atributos do Workday para o AD. Para cumprir as obrigações de privacidade do usuário, você pode garantir que nenhum dado seja retido nos logs de eventos por mais de 48 horas, configurando uma tarefa agendada do Windows para limpar o log de eventos.

O serviço de provisionamento do Azure AD se encaixa na categoria **processador de dados** da classificação do RGPD. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados para parceiros-chave e consumidores finais. O serviço de provisionamento do Azure AD não gera dados de usuário e não possui controle independente com relação aos dados pessoais coletados e como são utilizados. A recuperação de dados, agregação, análise e relatórios no Azure AD são baseados em dados empresariais existentes.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Em relação à retenção de dados, o serviço de provisionamento do Azure AD não gera relatórios, executa análise ou fornece informações por mais de 30 dias. Portanto, o serviço de provisionamento do Azure AD não armazena, processa ou retém quaisquer dados por mais de 30 dias. Este design está em conformidade com os regulamentos do GDPR, com os regulamentos de conformidade de privacidade da Microsoft e com as políticas de retenção de dados do Azure Active Directory.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os cenários de integração do Azure AD e do Workday e as chamadas de serviço Web](../app-provisioning/workday-integration-reference.md)
* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o Workday e o Azure Active Directory](workday-tutorial.md)
* [Saiba como configurar o Write-back do Workday](workday-writeback-tutorial.md)
* [Saiba como usar APIs do Microsoft Graph para gerenciar configurações de provisionamento](/graph/api/resources/synchronization-overview)
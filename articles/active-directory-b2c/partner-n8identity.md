---
title: Tutorial para configurar a identidade N8 com Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial para configurar a ferramenta de administração do TheAccessHub com Azure Active Directory B2C para atender à migração de contas de clientes e à administração do CSR (Customer Service Requests).
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170915"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Tutorial para configurar a ferramenta de administração do TheAccessHub com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como integrar o Azure Active Directory (AD) B2C com a [ferramenta de administração do TheAccessHub](https://n8id.com/products/theaccesshub-admintool/) da identidade do N8. Esta solução aborda a migração de contas de clientes e a administração do CSR (Customer Service Requests).  

Essa solução é adequada para você, se você tiver uma ou mais das seguintes necessidades:

- Você tem um site existente e deseja migrar para o Azure AD B2C. No entanto, você está lutando com a migração de suas contas de cliente, incluindo senhas

- Você precisa de uma ferramenta para o CSR administrar Azure AD B2C contas.

- Você tem a necessidade de usar a administração delegada para seus representantes de gerenciamento.

- Você deseja sincronizar e mesclar seus dados de vários repositórios em Azure AD B2C.

## <a name="pre-requisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](./tutorial-create-tenant.md). O locatário deve estar vinculado à sua assinatura do Azure.

- Um ambiente da ferramenta de administração do TheAccessHub: entre em contato com a [identidade N8](https://n8id.com/contact/) para provisionar um novo ambiente.

- Adicional Informações de conexão e credenciais para quaisquer bancos de dados ou LDAPs (Lightweight Directory Access Protocols) dos quais você deseja migrar o cliente.

- Adicional Configurado Azure AD B2C ambiente para usar [políticas personalizadas](./custom-policy-get-started.md), se você quiser integrar a ferramenta de administração do TheAccessHub ao seu fluxo de política de inscrição.

## <a name="scenario-description"></a>Descrição do cenário

A ferramenta de administração do TheAccessHub é executada como qualquer outro aplicativo no Azure. Ele pode ser executado em uma assinatura do Azure da identidade do N8 ou na assinatura do cliente. O diagrama de arquitetura a seguir mostra a implementação.

![Imagem mostrando o diagrama de arquitetura n8identity](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página de logon. Os usuários selecionam inscrever-se para criar uma nova conta e inserir informações na página. Azure AD B2C coleta os atributos de usuário.
| 2. | Azure AD B2C chama a ferramenta de administração TheAccessHub e passa os atributos de usuário
| 3. | A ferramenta de administração do TheAccessHub verifica o banco de dados existente quanto às informações do usuário atual.  
| 4. | O registro do usuário é sincronizado do banco de dados para a ferramenta de administração do TheAccessHub.
| 5. | A ferramenta de administração do TheAccessHub compartilha os dados com o administrador delegado do CSR/assistência técnica.
| 6. | A ferramenta de administração do TheAccessHub sincroniza os registros de usuário com Azure AD B2C.
| 7. |Com base na resposta de êxito/falha da ferramenta de administração do TheAccessHub, Azure AD B2C envia um email de boas-vindas personalizado para o usuário.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Criar um administrador global em seu locatário de Azure AD B2C

A ferramenta de administração do TheAccessHub requer permissões para agir em nome de um administrador global para ler informações do usuário e realizar alterações em seu locatário Azure AD B2C. Alterações em seus administradores regulares ganhas; t impacto a capacidade da ferramenta de administração do TheAccessHub de interagir com o locatário.

Para criar um administrador global, siga estas etapas:

1. No portal do Azure, entre no seu locatário do Azure AD B2C como um administrador. Navegue até **Azure Active Directory**  >  **usuários**
2. Selecionar **novo usuário**
3. Escolha **criar usuário** para criar um usuário de diretório regular e não um cliente
4. Concluir o formulário de informações de identidade

   a. Insira o nome de usuário como ' theaccesshub '

   b. Insira o nome, como ' conta de serviço TheAccessHub '

5. Selecione **Mostrar senha** e copie a senha inicial para uso posterior
6. Atribuir a função de administrador global

   a. Selecione o **usuário** de funções atuais do usuário para alterá-lo

   b. Verificar o registro do administrador global

   c. **Selecione**  >  **Criar**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Conectar a ferramenta de administração do TheAccessHub com seu locatário Azure AD B2C

A ferramenta de administração do TheAccessHub usa o API do Graph da Microsoft para ler e fazer alterações em seu diretório. Ele atua como um administrador global em seu locatário. A permissão adicional é necessária pela ferramenta de administração do TheAccessHub, que você pode conceder de dentro da ferramenta.

Para autorizar a ferramenta de administração do TheAccessHub a acessar seu diretório, siga estas etapas:

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até **administrador do sistema**  >  **Azure ad B2C configuração**

3. Selecionar **autorizar conexão**

4. Na nova janela, entre com sua conta de administrador global. Você poderá ser solicitado a redefinir sua senha se estiver entrando pela primeira vez com a nova conta de serviço.

5. Siga os prompts e selecione **aceitar** para conceder ao TheAccessHub admin Tool as permissões solicitadas.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>Configurar um novo usuário do CSR usando sua identidade corporativa

Crie um usuário do CSR/assistência técnica que acessa a ferramenta de administração do TheAccessHub usando suas credenciais de Azure Active Directory corporativa existentes.

Para configurar o usuário CSR/helpdesk com SSO (logon único), as seguintes etapas são recomendadas:

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8.

2. Navegue até **Ferramentas do Gerenciador**  >  **gerenciar colegas**

3. Selecione **Adicionar colega**

4. Selecione o **tipo de colega administrador do Azure**

5. Insira as informações de perfil do colega

   a. A escolha de uma organização doméstica controlará quem tem permissão para gerenciar esse usuário.

   b. Para ID de logon/nome de usuário do Azure AD, forneça o nome principal de usuário da conta de Azure Active Directory do usuário.

   c. Na guia funções do TheAccessHub, verifique o helpdesk da função gerenciada. Ele permitirá que o usuário acesse a exibição gerenciar colegas. O usuário ainda precisará ser colocado em um grupo ou ter um proprietário da organização para agir nos clientes.

6. Selecione **Enviar**.

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>Configurar um novo usuário do CSR usando uma nova identidade

Crie um usuário CSR/helpdesk que acessará a ferramenta de administração do TheAccessHub com uma nova credencial local exclusiva da ferramenta de administração do TheAccessHub. Isso será usado principalmente por organizações que não usam um Azure AD para sua empresa.

Para [configurar um usuário CSR/helpdesk](https://youtu.be/iOpOI2OpnLI) sem SSO, siga estas etapas:

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até **Ferramentas do Gerenciador**  >  **gerenciar colegas**

3. Selecione **Adicionar colega**

4. Selecione o **tipo de colega administrador local**

5. Insira as informações de perfil do colega

   a. A escolha de uma organização doméstica controlará quem tem permissão para gerenciar esse usuário.

   b. Na guia **funções do TheAccessHub** , selecione a **assistência técnica** de função gerenciada. Ele permitirá que o usuário acesse a exibição gerenciar colegas. O usuário ainda precisará ser colocado em um grupo ou ter um proprietário da organização para agir nos clientes.

6. Copie os atributos **ID de logon/email** e **senha de uso único** . Forneça para o novo usuário. Eles usarão essas credenciais para fazer logon na ferramenta de administração do TheAccessHub. O usuário será solicitado a inserir uma nova senha no primeiro logon.

7. Selecione **Enviar**

## <a name="configure-partitioned-csr-administration"></a>Configurar administração de CSR particionada

As permissões para gerenciar os usuários do cliente e do CSR/helpdesk na ferramenta de administração do TheAccessHub são gerenciadas com o uso de uma hierarquia da organização. Todos os colegas e clientes têm uma organização doméstica em que residem. Colegas ou grupos de colegas específicos podem ser atribuídos como proprietários de organizações.  Os proprietários da organização podem gerenciar (fazer alterações) colegas e clientes em organizações ou suborganizaçãos que eles possuem. Para permitir que vários colegas gerenciem um conjunto de usuários, um grupo pode ser criado com muitos membros. O grupo pode ser atribuído como um proprietário da organização e todos os membros do grupo podem gerenciar colegas e clientes na organização.

### <a name="create-a-new-group"></a>Criar um novo grupo

1. Faça logon na ferramenta de administração do TheAccessHub usando **as credenciais** fornecidas pela identidade do N8

2. Navegue até a **organização > gerenciar grupos**

3. Selecione > **Adicionar grupo**

4. Insira um **nome de grupo**, uma **Descrição do grupo** e um **proprietário do grupo**

5. Procure e marque as caixas nos colegas que você deseja que sejam membros do grupo e, em seguida, selecione >**Adicionar**

6. Na parte inferior da página, você pode ver todos os membros do grupo.

7. Se os membros necessários puderem ser removidos selecionando o **x** no final da linha

8. Selecione **Enviar**

### <a name="create-a-new-organization"></a>Criar uma nova organização

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até a organização > **gerenciar organizações**

3. Selecione > **Adicionar organização**

4. Forneça um **nome de organização**, **proprietário da organização** e **organização pai**.

    a. O nome da organização é ideal para um valor que corresponda aos dados do cliente. Ao carregar dados de colegas e clientes, se você fornecer o nome da organização na carga, o colega poderá ser colocado automaticamente na organização.

    b. O proprietário representa a pessoa ou o grupo que gerenciará os clientes e os colegas nesta organização e em qualquer suborganização no.

    c. A organização pai indica qual outra organização é inerentemente, também responsável por essa organização.

5. Selecione **Enviar**.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>Modificar a hierarquia por meio do modo de exibição de árvore

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até **Manager Tools** a  >  **exibição de árvore** de ferramentas do Gerenciador

3. Nessa representação, você pode visualizar quais colegas e grupos podem gerenciar quais organizações.

4. As hierarquias podem ser modificadas arrastando organizações GDR sobre organizações nas quais você deseja que elas sejam pais.

5. Selecione **salvar** quando terminar de alterar a hierarquia.

## <a name="customize-welcome-notification"></a>Personalizar notificação de boas-vindas

Enquanto você estiver usando o TheAccessHub para migrar usuários de uma solução de autenticação anterior para o Azure AD B2C, convém personalizar a notificação de boas-vindas do usuário, que é enviada ao usuário pelo TheAccessHub durante a migração. Essa mensagem normalmente inclui o link para o cliente definir uma nova senha no diretório Azure AD B2C.

Para personalizar a notificação:

1. Faça logon no TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegar para notificações de **administrador do sistema**  >  **Notifications**

3. Selecione o **modelo criar colega**

4. Selecionar **Editar**

5. Altere os campos de mensagem e modelo conforme necessário. O campo de modelo tem reconhecimento de HTML e pode enviar notificações formatadas em HTML para emails do cliente.

6. Selecione **salvar** quando terminar.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Migrar dados de fontes de dados externas para Azure AD B2C

Usando a ferramenta de administração do TheAccessHub, você pode importar dados de vários bancos de dado, LDAPs e arquivos CSV e, em seguida, enviar esses dados para o locatário Azure AD B2C. Isso é feito com o carregamento de dados no tipo de colega do usuário Azure AD B2C na ferramenta de administração do TheAccessHub.  Se a fonte de dados não for o próprio Azure, os dados serão colocados em ambas as ferramentas de administração do TheAccessHub e Azure AD B2C. Se a origem de seus dados externos não for um arquivo. csv simples em seu computador, configure uma fonte de dados antes de fazer o carregamento de dados. As etapas a seguir descrevem como criar uma fonte de dados e fazer o carregamento de dados.

### <a name="configure-a-new-data-source"></a>Configurar uma nova fonte de dados

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até **System admin**  >  **Data Sources**

3. Selecione **Adicionar fonte de dados**

4. Forneça um **nome** e um **tipo** para esta fonte de dados

5. Preencha os dados do formulário, dependendo do tipo de fonte de dados:

   **Para bancos de dados**

   a. **Tipo** – banco de dados

   b. **Tipo de banco de dados** – selecione um banco de dados de um dos tipos de banco de dados com suporte.

   c. **URL de conexão** – Insira uma cadeia de conexão JDBC bem formatada. Como: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Nome de usuário** – Insira o nome de usuário para acessar o banco de dados

   e. **Senha** – Insira a senha para acessar o banco de dados

   f. **Consulta** – Insira a consulta SQL para extrair os detalhes do cliente. Como: ``SELECT * FROM mytable;``

   g. Selecione **testar conexão**, você verá um exemplo de seus dados para garantir que a conexão esteja funcionando.

   **Para LDAPs**

   a. **Tipo** – LDAP

   b. **Host** – Insira o nome do host ou IP do computador no qual o servidor LDAP está em execução. Como: ``mysite.com``

   c. **Porta** – Insira o número da porta na qual o servidor LDAP está escutando.

   d. **SSL** – marque a caixa se a ferramenta de administração do TheAccessHub deve se comunicar com o LDAP com segurança usando SSL. Usar SSL é altamente recomendável.

   e. **DN de logon** – Insira o DN da conta de usuário para fazer logon e faça a pesquisa LDAP

   f. **Senha** – Insira a senha do usuário

   g. **DN de base** – Insira o DN na parte superior da hierarquia na qual você deseja fazer a pesquisa

   h. **Filtro** – Insira a cadeia de caracteres de filtro LDAP, que obterá os registros do cliente

   i. **Atributos** – Insira uma lista de atributos separados por vírgulas dos registros de cliente a serem passados para a ferramenta de administração do TheAccessHub

   j. Selecione a **conexão de teste**, você verá um exemplo de seus dados para garantir que a conexão esteja funcionando.

   **Para OneDrive**

   a. **Tipo** – onedrive for Business

   b. Selecionar **autorizar conexão**

   c. Uma nova janela solicitará que você faça logon no **onedrive**, faça logon com um usuário com acesso de leitura à sua conta do onedrive. TheAccessHub admin Tool, agirá para esse usuário ler arquivos de carregamento de CSV.

   d. Siga os prompts e selecione **aceitar** para conceder ao TheAccessHub admin Tool as permissões solicitadas.

6. Selecione **salvar** quando terminar.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Sincronizar dados de sua fonte de dados no Azure AD B2C

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até **administrador de sistema**  >  **sincronização de dados**

3. Selecionar **nova carga**

4. Selecione o **tipo de colega** Azure ad B2C usuário

5. Selecione **origem**, na caixa de diálogo pop-up, selecione sua fonte de dados. Se você criou uma fonte de dados do OneDrive, selecione também o arquivo.

6. Se você não quiser criar novas contas de clientes com essa carga, altere a primeira política: **se o colega não for encontrado em TheAccessHub, então** **não faça nada**

7. Se você não quiser atualizar contas de clientes existentes com essa carga, altere a segunda política **se a fonte e a incompatibilidade de dados TheAccessHub** **não fizerem nada**

8. Selecione **Avançar**

9. Na **configuração de mapeamento de pesquisa**, identificaremos como correlacionar os registros de carga com os clientes já carregados na ferramenta de administração do TheAccessHub. Escolha um ou mais atributos de identificação na origem. Corresponda aos atributos com um atributo na ferramenta de administração TheAccessHub que contém os mesmos valores. Se uma correspondência for encontrada, o registro existente será substituído; caso contrário, um novo cliente será criado. Você pode sequenciar várias verificações. Por exemplo, você pode verificar o email primeiro e, em seguida, o nome e sobrenome.

10. No menu do lado esquerdo, selecione mapeamento de **dados**.

11. Na configuração do Data-Mapping, atribua quais atributos da ferramenta de administração do TheAccessHub devem ser preenchidos dos seus atributos de origem. Não é necessário mapear todos os atributos. Os atributos não mapeados permanecerão inalterados para os clientes existentes.

12. Se você mapear para o atributo org_name com um valor que seja o nome de uma organização existente, os novos clientes criados serão colocados nessa organização.

13. Selecione **Avançar**

14. Uma agenda diária/semanal ou mensal poderá ser especificada se essa carga deve ocorrer novamente. Caso contrário, mantenha o padrão **agora**.

15. Selecione **Enviar**

16. Se a **agenda agora** tiver sido selecionada, um novo registro será adicionado à tela de sincronizações de dados imediatamente. Depois que a fase de validação tiver atingido 100%, selecione o **novo registro** para ver o resultado esperado para a carga. Para cargas agendadas, esses registros só serão exibidos após a hora agendada.

17. Se não houver erros, selecione **executar** para confirmar as alterações. Caso contrário, selecione **remover** no menu **mais** para remover a carga. Em seguida, você pode corrigir os dados de origem ou os mapeamentos de carga e tentar novamente. Em vez disso, se o número de erros for pequeno, você poderá atualizar manualmente os registros e selecionar **Atualizar** em cada registro para fazer as correções. Por fim, você pode continuar com quaisquer erros e resolvê-los posteriormente como **suporte a intervenções** na ferramenta de administração do TheAccessHub.

18. Quando o registro de **sincronização de dados** se tornar 100% na fase de carga, todas as alterações resultantes da carga serão iniciadas. Os clientes devem começar a aparecer ou a receber alterações em Azure AD B2C.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Sincronizar Azure AD B2C dados do cliente 

Como uma operação única ou em andamento, a ferramenta de administração do TheAccessHub pode sincronizar todas as informações do cliente de Azure AD B2C para a ferramenta de administração do TheAccessHub. Isso garante que os administradores do CSR/helpdesk estejam vendo informações atualizadas sobre o cliente.

Para sincronizar dados do Azure AD B2C para a ferramenta de administração do TheAccessHub:

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até **administrador de sistema**  >  **sincronização de dados**

3. Selecionar **nova carga**

4. Selecione o **tipo de colega** Azure ad B2C usuário

5. Para a etapa de **Opções** , deixe os padrões.

6. Selecione **Avançar**

7. Para o **mapeamento de dados &** etapa de pesquisa, deixe os padrões. Exceto se você mapear para o atributo **org_name** com um valor que seja o nome de uma organização existente, os novos clientes criados serão colocados nessa organização.

8. Selecione **Avançar**

9. Uma agenda diária/semanal ou mensal poderá ser especificada se essa carga deve ocorrer novamente. Caso contrário, mantenha o padrão: **agora**. É recomendável sincronizar de Azure AD B2C regularmente.

10. Selecione **Enviar**

11. Se a agenda **agora** tiver sido selecionada, um novo registro será adicionado à tela de sincronizações de dados imediatamente. Depois que a fase de validação tiver atingido 100%, selecione o novo registro para ver o resultado esperado para a carga. Para cargas agendadas, esses registros só serão exibidos após a hora agendada.

12. Se não houver erros, selecione **executar** para confirmar as alterações. Caso contrário, selecione **remover** no menu mais para remover a carga. Em seguida, você pode corrigir os dados de origem ou os mapeamentos de carga e tentar novamente. Em vez disso, se o número de erros for pequeno, você poderá atualizar manualmente os registros e selecionar **Atualizar** em cada registro para fazer as correções. Por fim, você pode continuar com quaisquer erros e resolvê-los posteriormente como suporte a intervenções na ferramenta de administração do TheAccessHub.

13. Quando o registro de **sincronização de dados** se tornar 100% na fase de carga, todas as alterações resultantes da carga serão iniciadas.

## <a name="configure-azure-ad-b2c-policies"></a>Configurar políticas de Azure AD B2C

Ocasionalmente, a sincronização da ferramenta de administração do TheAccessHub é limitada em sua capacidade de manter seu estado atualizado com Azure AD B2C. Podemos aproveitar a API da ferramenta de administração do TheAccessHub e as políticas de Azure AD B2C para informar à ferramenta de administração do TheAccessHub as alterações conforme elas acontecem. Essa solução requer conhecimento técnico de [Azure ad B2C políticas personalizadas](./custom-policy-get-started.md). Na próxima seção, daremos um exemplo de etapas de política e um certificado seguro para notificar a ferramenta de administrador do TheAccessHub de novas contas em suas políticas personalizadas do Sign-Up.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Criar uma credencial segura para invocar a API da ferramenta de administração do TheAccessHub

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até **System admin**  >  **Admin Tools**  >  **API segurança**

3. Selecione **gerar**

4. Copiar a **senha do certificado**

5. Selecione **baixar** para obter o certificado do cliente.

6. Siga este [tutorial](./secure-rest-api.md#https-client-certificate-authentication ) para adicionar o certificado do cliente ao Azure ad B2C.

### <a name="retrieve-your-custom-policy-examples"></a>Recuperar seus exemplos de política personalizada

1. Faça logon na ferramenta de administração do TheAccessHub usando as credenciais fornecidas pela identidade do N8

2. Navegue até **sistema administração**  >  **administrador ferramentas**  >  **políticas do Azure B2C**

3. Forneça seu domínio de locatário do Azure AD B2C e as duas IDs da estrutura de experiência de identidade da sua configuração de estrutura de experiência de identidade

4. Selecione **Salvar**

5. Selecione **baixar** para obter um arquivo zip com políticas básicas que adicionam clientes à ferramenta de administração do TheAccessHub enquanto os clientes se inscrevem.

6. Siga este [tutorial](./custom-policy-get-started.md) para começar a criar políticas personalizadas no Azure ad B2C.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
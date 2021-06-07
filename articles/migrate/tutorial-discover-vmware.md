---
title: 'Descobrir servidores em execução no ambiente do VMware com a ferramenta Migrações para Azure: descoberta e avaliação'
description: 'Saiba como descobrir servidores locais em execução no ambiente do VMware com a ferramenta de Migrações para Azure: descoberta e avaliação'
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: 09b04c67519bfa920a3781612823c5755cbc6d2d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627788"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Descobrir servidores em execução no ambiente do VMware com a ferramenta Migrações para Azure: descoberta e avaliação

Como parte de sua jornada de migração para o Azure, descubra seu inventário local e suas cargas de trabalho.

Este tutorial mostra como descobrir servidores em execução no ambiente do VMware com a ferramenta Migrações para Azure: Descoberta e avaliação usando um dispositivo leve das Migrações para Azure. Implante o dispositivo como um servidor em execução no seu vCenter Server para descobrir continuamente os servidores e seus metadados de desempenho, aplicativos em execução em servidores, dependências de servidor e instâncias de SQL Server e bancos de dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma conta do Azure.
> * Prepare o ambiente de VMware para descoberta.
> * Criar um projeto.
> * Configurar o dispositivo das Migrações para Azure.
> * Iniciar a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam as opções padrão sempre que possível.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se estes pré-requisitos estão em vigor.

**Requisito** | **Detalhes**
--- | ---
**Host de vCenter Server/ESXi** | Será necessário obter um vCenter Server executando a versão 5.5, 6.0, 6.5 ou 6.7.<br/><br/> Os servidores precisam ser hospedados em um host ESXi executando a versão 5.5 ou posterior.<br/><br/> No vCenter Server, permita conexões de entrada na porta TCP 443, de modo que o dispositivo possa coletar os metadados de configuração e desempenho.<br/><br/> O dispositivo é conectado ao vCenter Server na porta 443 por padrão. Se o vCenter Server escutar outra porta, modifique a porta ao fornecer os detalhes do vCenter Server no gerenciador de configurações do dispositivo.<br/><br/> Nos hosts ESXi, verifique se o acesso de entrada é permitido na porta TCP 443 para executar a descoberta de aplicativos instalados e a análise de dependência sem agente nos servidores.
**Dispositivo** | O vCenter Server precisa de recursos com o objetivo de alocar um servidor para o dispositivo das Migrações para Azure:<br/><br/> – 32 GB de RAM, 8 vCPUs e cerca de 80 GB de armazenamento em disco.<br/><br/> – Um comutador virtual externo e acesso à Internet no servidor do dispositivo, diretamente ou por um proxy.
**Servidores** | Em todas as versões do sistema operacional Windows e Linux, há suporte para a descoberta de metadados de configuração e desempenho. <br/><br/> Para executar a descoberta de aplicativos nos servidores, há suporte para todas as versões dos sistemas operacionais Windows e Linux. Acesse [aqui](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) para obter as versões do sistema operacional compatíveis com a análise de dependência sem agente.<br/><br/> Para executar a descoberta de aplicativos instalados e a análise de dependência sem agente, as ferramentas do VMware (mais tarde que 10.2.0) precisam ser instaladas e executadas em servidores. Os servidores do Windows precisam ter o PowerShell versão 2.0 ou posterior instalado.<br/><br/> Para descobrir instâncias e bancos de dados do SQL Server, verifique [aqui](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) as versões e as edições do SQL Server com suporte, as versões do sistema operacional Windows e os mecanismos de autenticação com suporte.

## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de usuário do Azure

Para criar um projeto e registrar o dispositivo de Migrações para Azure, você precisará ter uma conta com:

- Permissões de Colaborador ou Proprietário na assinatura do Azure
- Permissões para registrar aplicativos do AAD (Azure Active Directory)
- Permissões de Proprietário ou Colaborador, além de permissões de Administrador de Acesso do Usuário, na assinatura do Azure para criação de um Key Vault, usado durante a migração sem agente do servidor

Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura. Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir as permissões da seguinte maneira:

1. No portal do Azure, pesquise por "assinaturas" e, em **Serviços**, selecione **Assinaturas**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Caixa de pesquisa para pesquisar a assinatura do Azure":::


2. Na página **Assinaturas**, selecione a assinatura na qual deseja criar um projeto.
3. Na assinatura, selecione **Controle de acesso (IAM)**  > **Verificar o acesso**.
4. Em **Verificar o acesso**, procure a conta de usuário relevante.
5. Em **Adicionar uma atribuição de função**, clique em **Adicionar**.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Pesquisar uma conta de usuário para verificar o acesso e atribuir uma função":::
    
6. Em **Adicionar atribuição de função**, selecione a função Colaborador ou Proprietário e selecione a conta (azmigrateuser em nosso exemplo). Em seguida, clique em **Salvar**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Abre a página Adicionar atribuição de função para atribuir uma função à conta":::

1. Para registrar o dispositivo, sua conta do Azure precisa ter **permissões para registrar aplicativos do AAD.**
1. No portal do Azure, acesse **Azure Active Directory** > **Usuários** > **Configurações de Usuário**.
1. Em **Configurações de usuário**, verifique se os usuários do Azure AD podem registrar aplicativos (definido como **Sim** por padrão).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Verificar nas Configurações de Usuário se os usuários podem registrar aplicativos do Active Directory":::

9. Caso as configurações de 'Registros de aplicativo' estejam definidas como 'Não', solicite ao administrador global/de locatários a atribuição da permissão necessária. Como alternativa, o administrador global/de locatários pode atribuir a função **Desenvolvedor de aplicativos** a uma conta para permitir o registro do Aplicativo do AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Preparar o VMware

No vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA. Isso é necessário quando você implanta o dispositivo das Migrações para Azure como uma VM do VMware usando um arquivo OVA.

As Migrações para Azure precisam de uma conta somente leitura do vCenter Server para descoberta e avaliação de servidores em execução no seu ambiente VMware. Se você também quiser realizar a descoberta dos aplicativos instalados e a análise de dependências sem agente, a conta precisará ter privilégios habilitados para **Máquinas Virtuais > Operações de Convidado**.

### <a name="create-an-account-to-access-vcenter"></a>Criar uma conta para acessar o vCenter

No Cliente Web vSphere, configure uma conta da seguinte maneira:

1. No Cliente Web vSphere > selecione **Administração** usando uma conta com privilégios de administrador.
2. Em **Acesso**, selecione **Usuários e Grupos de SSO**.
3. Em **Usuários**, adicione um novo usuário.
4. Em **Novo Usuário**, digite os detalhes da conta. Em seguida, clique em **OK**.
5. Em **Permissões Globais**, selecione a conta de usuário e atribua a função **Somente Leitura** à conta. Em seguida, clique em **OK**.
6. Se você também quiser realizar a descoberta de aplicativos instalados e a análise de dependências sem servidor, acesse **Funções** > selecione a função **Somente leitura** e, em **Privilégios**, escolha **Operações de Convidado**. Propague os privilégios para todos os objetos no vCenter Server marcando a caixa de seleção "Propagar para filhos".

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Use a caixa de seleção para permitir operações de convidado na função Somente Leitura":::

> [!NOTE]
> Você pode limitar a descoberta a data centers vCenter Server específicos, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou servidores individuais definindo o escopo da conta do vCenter Server. [**Saiba mais**](set-discovery-scope.md) sobre como definir o escopo da conta de usuário do vCenter Server.

### <a name="create-an-account-to-access-servers"></a>Criar uma conta para acessar os servidores

Você precisa de uma conta de usuário com os privilégios necessários nos servidores para executar a descoberta de aplicativos instalados, a análise de dependências sem agente e a descoberta de instâncias e bancos de dados do SQL Server. Você pode fornecer a conta de usuário no gerenciador de configurações do dispositivo. O dispositivo não instala nenhum agente nos servidores.

1. Para servidores do Windows, crie uma conta (local ou domínio) com permissões administrativas nos servidores. Para descobrir instâncias e bancos de dados do SQL Server, você precisa que a conta do Windows ou do SQL Server seja membro da função de servidor sysadmin. [Saiba mais](/sql/relational-databases/security/authentication-access/server-level-roles) sobre como atribuir a função necessária à conta de usuário.
2. Para servidores Linux, crie uma conta com privilégios de raiz. Como alternativa, você pode criar uma conta com estas permissões nos arquivos /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

> [!NOTE]
> Agora você pode adicionar várias credenciais de servidor no Configuration Manager para executar a descoberta de aplicativos instalados, a análise de dependências sem agente e a descoberta de instâncias e bancos de dados do SQL Server. Você pode adicionar várias credenciais de autenticação de domínio/Windows (não domínio)/Linux (não domínio) e/ou SQL Server. [**Saiba mais**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Configurar um projeto

Configure um novo projeto.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão geral** > selecione uma opção de acordo com suas metas de migração: **Windows, Linux e SQL Server** ou **SQL Server (somente)** ou **Explorar mais cenários** > selecione **Criar projeto**.
5. Em **Criar projeto**, selecione sua assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Caixas para nome e região do projeto":::

7. Selecione **Criar**.
8. Aguarde alguns minutos até que o projeto seja implantado. A ferramenta **Migrações para Azure: descoberta e avaliação** é adicionada por padrão ao novo projeto.

> [!NOTE]
> Se você já tiver criado um projeto, use o mesmo projeto para registrar dispositivos adicionais a fim de descobrir e avaliar um número maior de servidores.[**Saiba mais**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurar o dispositivo

O recurso Migrações para Azure: Descoberta e avaliação usa um dispositivo leve de Migrações para Azure. O dispositivo executa a descoberta de servidores e envia os metadados de configuração e desempenho dos servidores às Migrações para Azure. Ele pode ser configurado pela implantação de um modelo OVA que pode ser baixado do projeto.

> [!NOTE]
> Se, por alguma razão, você não conseguir configurar o dispositivo usando o modelo, configure-o usando um script do PowerShell em um servidor Windows Server 2016 existente. [**Saiba mais**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>Implantar usando um modelo OVA

Para configurar o dispositivo usando um modelo OVA, siga as etapas abaixo:

1. Forneça um nome de dispositivo e gere uma chave do projeto no portal.
1. Baixe um arquivo de modelo OVA e importe-o para o vCenter Server. Verifique se o OVA é seguro.
1. Crie o dispositivo com base no arquivo OVA e verifique se ele pode se conectar às Migrações para Azure.
1. Configure o dispositivo pela primeira vez e registre-o no projeto usando a chave do projeto.

### <a name="1-generate-the-project-key"></a>1. Gerar a chave do projeto

1. Em **Metas de Migração** > **Windows, Linux e SQL Server** > **Migrações para Azure: descoberta e avaliação**, selecione **Descobrir**.
2. Em **Descobrir servidores** > **Seus servidores são virtualizados?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3. Em **1: Gerar chave do projeto**, forneça um nome para o dispositivo de Migrações para Azure que você vai configurar para a descoberta de servidores em seu ambiente do VMware. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página Descobrir durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave do projeto** será gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="2-download-the-ova-template"></a>2. Baixar o modelo OVA

Em **2: Baixar o dispositivo das Migrações para Azure**, selecione o arquivo .OVA e clique em **Baixar**.

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro antes de implantá-lo:

1. No servidor no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exemplo de uso: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Verifique as versões mais recentes do dispositivo e os valores de hash:

    - Para a nuvem pública do Azure:
    
        **Algoritmo** | **Download** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Para o Azure Government:
    
        **Algoritmo** | **Download** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3. Criar o servidor do dispositivo

Importar o arquivo baixado e criar um servidor no ambiente VMware

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.
2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para o servidor. Selecione o objeto de inventário no qual o servidor será hospedado.
5. Em **Host/Cluster**, especifique o host ou o cluster no qual o servidor será executado.
6. Em **Armazenamento**, especifique o destino de armazenamento para o servidor.
7. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
8. Em **Mapeamento de Rede**, especifique a rede à qual o servidor se conectará. A rede precisa de conectividade com a Internet para poder enviar metadados para as Migrações para Azure.
9. Revise e confirme as configurações e clique em **Concluir**.

### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se o servidor do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [**script do PowerShell**](deploy-appliance-script.md) em vez do OVA baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No console do cliente do vSphere, clique com o botão direito do mouse no servidor e selecione **Abrir Console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar ao dispositivo e abra a URL do Configuration Manager do dispositivo: `https://appliance name or IP address: 44368`.

   Como alternativa, você pode abrir o Configuration Manager na área de trabalho do servidor de dispositivo selecionando o atalho para o Configuration Manager.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No Configuration Manager > **Configurar pré-requisitos**, siga estas etapas:
   - **Conectividade**: o dispositivo verifica se o servidor tem acesso à Internet. Se o servidor usar um proxy:
     - Clique em **Configurar proxy** para especificar o endereço de proxy `http://ProxyIPAddress` ou `http://ProxyFQDN` e a porta de escuta.
     - Especifique as credenciais caso o proxy exija autenticação.
     - Há suporte apenas para o proxy HTTP.
     - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
   - **Sincronização do horário**: O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
   - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas. Após a conclusão da verificação, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos serviços em execução no servidor de dispositivo.
   - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6.7 do VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo, conforme fornecido nas **Instruções de instalação**.

     A Migração de Servidor das Migrações para Azure usa o VDDK para replicar servidores durante a migração para o Azure. 
1. Se desejar, você poderá **executar os pré-requisitos novamente** a qualquer momento durante a configuração do dispositivo para verificar se ele atende a todos os pré-requisitos.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Painel 1 no Configuration Manager do dispositivo":::

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto** copiada do portal. Se você não tiver a chave, acesse **Migrações para Azure: descoberta e avaliação > Descobrir > Gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração da chave e copie a chave correspondente.
1. Será necessário um código do dispositivo para a autenticação com o Azure. Clicar em **Logon** abrirá uma janela restrita com o código do dispositivo, conforme mostrado abaixo.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Janela restrita mostrando o código do dispositivo":::

1. Clique em **Copiar código e Fazer logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, cole o código do dispositivo e conecte-se usando seu nome de usuário do Azure e a senha.
   
   Não há suporte para a entrada com um PIN.
3. Caso feche a guia de logon por engano sem fazer logon, você precisará atualizar a guia do navegador do gerenciador de configuração do dispositivo para habilitar novamente o botão Logon.
1. Depois de fazer logon com êxito, volte para a guia anterior usando o gerenciador de configuração do dispositivo.
1. Se a conta de usuário do Azure usada para o registro em log tiver as permissões corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Painel 2 no Configuration Manager do dispositivo":::

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

### <a name="provide-vcenter-server-details"></a>Forneça os detalhes do vCenter Server

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho dos servidores.

1. Na **Etapa 1: Fornecer as credenciais do vCenter Server**, clique em **Adicionar credenciais** para especificar um nome amigável para as credenciais, adicione o **Nome de usuário** e a **Senha** para a conta do vCenter Server que o dispositivo usará para descobrir os servidores em execução no vCenter Server.
    - Você deve ter configurado uma conta com as permissões necessárias, conforme abordado no artigo acima.
    - Se você quiser definir o escopo da descoberta para objetos específicos do VMware (data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou servidores individuais do vCenter Server), examine as instruções [neste artigo](set-discovery-scope.md) para restringir a conta usada por Migrações para Azure.
1. Na **Etapa 2: Fornecer detalhes do vCenter Server**, clique em **Adicionar origem da descoberta** para selecionar o nome amigável das credenciais na lista suspensa e especifique o **Endereço IP/FQDN** do vCenter Server. Você pode deixar a **Porta** como padrão (443) ou especificar uma porta personalizada na qual o vCenter Server escuta e clique em **Salvar**.
1. Quando você clicar em **Salvar**, o dispositivo tentará validar a conexão com o vCenter Server com as credenciais fornecidas e mostrará o **Status de validação** na tabela com relação ao endereço IP/FQDN do vCenter Server.
1. Você pode **revalidar** a conectividade com o vCenter Server a qualquer momento antes de iniciar a descoberta.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Painel 3 no Configuration Manager do dispositivo para obter os detalhes do vCenter Server":::

### <a name="provide-server-credentials"></a>Fornecer credenciais do servidor

Na **Etapa 3: Fornecer credenciais de servidor para executar o inventário de software, a análise de dependências sem agente e a descoberta de instâncias e bancos de dados do SQL Server**, você pode optar por fornecer várias credenciais de servidor ou, se não quiser usar esses recursos, poderá optar por ignorar a etapa e prosseguir com a descoberta do vCenter Server. Você poderá alterar a intenção a qualquer momento posterior.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Painel 3 no Configuration Manager do dispositivo para obter detalhes do servidor":::

Se quiser usar esses recursos, você poderá fornecer as credenciais do servidor seguindo as etapas abaixo. O dispositivo tentará mapear automaticamente as credenciais para os servidores para realizar os recursos de descoberta.

- Você pode adicionar credenciais de servidor clicando no botão **Adicionar Credenciais**. Isso abrirá uma janela restrita na qual você pode escolher o **Tipo de credenciais** no menu suspenso.
- Você pode fornecer credenciais de autenticação de domínio/Windows (não domínio)/Linux (não domínio)/SQL Server. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as manipulamos.​
- Para cada tipo de credenciais, você precisa especificar um nome amigável para credenciais, adicionar um **nome de usuário** e uma **senha** e clicar em **Salvar**.
- Se você escolher credenciais de domínio, também precisará especificar o FQDN para o domínio. O FQDN é necessário para validar a autenticidade das credenciais com o Active Directory desse domínio.
- Examine as [permissões necessárias](add-server-credentials.md#required-permissions) na conta para a descoberta de aplicativos instalados, a análise de dependências sem agente ou para a descoberta de instâncias e bancos de dados do SQL Server.
- Se desejar adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para salvar e adicionar mais credenciais.
- Quando você clica em **Salvar** ou **Adicionar mais**, o dispositivo valida as credenciais de domínio com o Active Directory do domínio para a autenticidade delas. Isso é feito para evitar bloqueios de conta quando o dispositivo executa várias iterações para mapear credenciais para os respectivos servidores.
- Você pode ver o **Status de validação** de todas as credenciais de domínio na tabela de credenciais. Somente as credenciais de domínio serão validadas.
- Se a validação falhar, você poderá clicar no status **Com falha** para ver o erro encontrado e clicar em **Revalidar credenciais** após corrigir o problema para validar as credenciais de domínio com falha novamente.

     :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Painel 3 no Gerenciador de configuração do dispositivo para o fornecimento de várias credenciais":::

### <a name="start-discovery"></a>Iniciar descoberta

1. Clique em **Iniciar descoberta** para iniciar a descoberta do vCenter Server. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta com relação ao endereço IP/FQDN do vCenter Server na tabela de origens.
1. Se você tiver fornecido credenciais de servidor, o inventário de software (descoberta de aplicativos instalados) será iniciado automaticamente após a conclusão da descoberta do vCenter Server. O inventário de software é executado uma vez a cada 12 horas.
1. O [inventário de software](how-to-discover-applications.md) identifica as instâncias do SQL Server sendo executadas nos servidores e usando as informações, as tentativas do dispositivo de se conectar às instâncias por meio da autenticação do Windows ou das credenciais de autenticação do SQL Server fornecidas no dispositivo e coletar dados nos bancos do SQL Server e as propriedades deles. A descoberta do SQL é realizada uma vez a cada 24 horas.
1. Durante o inventário de software, as credenciais dos servidores adicionados serão iteradas em relação aos servidores e validadas para análise de dependências sem agente. Você pode habilitar a análise de dependências sem agente para servidores do portal. Somente os servidores em que a validação é realizada com sucesso podem ser selecionados para habilitar a análise de dependências sem agente.

> [!Note]
>As Migrações para Azure criptografarão a comunicação entre o dispositivo das Migrações para Azure e as instâncias do SQL Server de origem (com a propriedade de conexão Encrypt definida como TRUE). Essas conexões são criptografadas com [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (definido como TRUE); a camada de transporte usará o SSL para criptografar o canal e ignorar a cadeia de certificados para validar a confiança. O servidor do dispositivo precisa ser configurado para [**confiar na autoridade raiz do certificado**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Se nenhum certificado tiver sido provisionado no servidor quando ele foi inicializado, o SQL Server vai gerar um certificado autoassinado usado para criptografar pacotes de logon. [**Saiba mais**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

A descoberta funciona da seguinte maneira:

- São necessários cerca de 15 minutos para que o inventário de servidores descobertos seja exibido no portal.
- A descoberta de aplicativos instalados pode levar algum tempo. A duração depende do número de servidores descobertos. Para 500 servidores, leva aproximadamente uma hora para o inventário de aplicativos aparecer no portal das Migrações para Azure.
- Depois que a descoberta de servidores for concluída, habilite a análise de dependências sem agente nos servidores do portal.
- As instâncias e os dados de bancos de dados do SQL Server começarão a aparecer no portal dentro de 24 horas do início da descoberta.

## <a name="next-steps"></a>Próximas etapas

- [Avaliar os servidores](./tutorial-assess-vmware-azure-vm.md) para migração para VMs do Azure.
- [Avaliar os SQL Servers](./tutorial-assess-sql.md) para migração para o SQL do Azure.
- [Examinar os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta.

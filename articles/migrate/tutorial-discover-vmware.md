---
title: Descobrir VMs de VMware com a Avaliação de Servidor das Migrações para Azure
description: Saiba como descobrir VMs de VMware locais com a ferramenta de Avaliação de Servidor das Migrações para Azure
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 0e06d82c30743a4084cfc5ff856b4a9c8d548146
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98566939"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Tutorial: Descobrir VMs de VMware com a Avaliação de Servidor

Como parte de seu percurso de migração para o Azure, descubra o inventário local e as cargas de trabalho.

Este tutorial mostrará como descobrir VMs (máquinas virtuais) de VMware locais com as Migrações para Azure: usando a ferramenta de Avaliação de Servidor e um dispositivo leve das Migrações para Azure. Você implantará o dispositivo como uma VM do VMware para descobrir continuamente as VMs e os respectivos metadados de desempenho, os aplicativos em execução nas VMs e as dependências de VM.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma conta do Azure.
> * Prepare o ambiente de VMware para descoberta.
> * Criar um projeto do Migrações para Azure.
> * Configurar o dispositivo das Migrações para Azure.
> * Iniciar a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam as opções padrão sempre que possível.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se estes pré-requisitos estão em vigor.


**Requisito** | **Detalhes**
--- | ---
**Host de vCenter Server/ESXi** | Será necessário obter um vCenter Server executando a versão 5.5, 6.0, 6.5 ou 6.7.<br/><br/> As VMs deverão ser hospedadas em um host ESXi executando a versão 5.5 ou posterior.<br/><br/> No vCenter Server, permita conexões de entrada na porta TCP 443, de modo que o dispositivo possa coletar os metadados de configuração e desempenho.<br/><br/> O dispositivo é conectado ao vCenter na porta 443 por padrão. Se o vCenter Server escutar outra porta, modifique a porta ao fornecer os detalhes do vCenter Server no gerenciador de configurações do dispositivo.<br/><br/> No servidor ESXi que hospeda as VMs, verifique se o acesso de entrada é permitido na porta TCP 443 para descobrir os aplicativos instalados nas VMs e as dependências de VM.
**Dispositivo** | O vCenter Server precisará de recursos com o objetivo de alocar uma VM para o dispositivo das Migrações para Azure:<br/><br/> – 32 GB de RAM, 8 vCPUs e cerca de 80 GB de armazenamento em disco.<br/><br/> – Um comutador virtual externo e acesso à Internet na VM do dispositivo, diretamente ou por um proxy.
**VMs** | Em todas as versões do sistema operacional Windows e Linux, há suporte para a descoberta de metadados de configuração e desempenho, bem como para a descoberta de aplicativos instalados nas VMs. <br/><br/> Acesse [aqui](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) para obter as versões do sistema operacional compatíveis com a análise de dependência sem agente.<br/><br/> Para descobrir os aplicativos instalados e as dependências de VM, as Ferramentas do VMware (versões posteriores a 10.2.0) precisam ser instaladas e executadas nas VMs, e as VMs do Windows precisam ter o PowerShell versão 2.0 ou posterior instalado.


## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de usuário do Azure

Para criar um projeto das Migrações para Azure e registrar o dispositivo de Migrações para Azure, você precisa de uma conta com:
- Permissões de Colaborador ou Proprietário na assinatura do Azure
- Permissões para registrar aplicativos do AAD (Azure Active Directory)
- Permissões de Proprietário ou Colaborador, além de permissões de Administrador de Acesso do Usuário, na assinatura do Azure para criação de um Key Vault, usado durante a migração sem agente do VMware

Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura. Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir as permissões da seguinte maneira:

1. No portal do Azure, pesquise por "assinaturas" e, em **Serviços**, selecione **Assinaturas**.

    ![Caixa de pesquisa para pesquisar a assinatura do Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Na página **Assinaturas**, selecione a assinatura na qual você deseja criar um projeto das Migrações para Azure. 
3. Na assinatura, selecione **Controle de acesso (IAM)**  > **Verificar o acesso**.
4. Em **Verificar o acesso**, procure a conta de usuário relevante.
5. Em **Adicionar uma atribuição de função**, clique em **Adicionar**.

    ![Pesquisar uma conta de usuário para verificar o acesso e atribuir uma função](./media/tutorial-discover-vmware/azure-account-access.png)

6. Em **Adicionar atribuição de função**, selecione a função Colaborador ou Proprietário e selecione a conta (azmigrateuser em nosso exemplo). Em seguida, clique em **Salvar**.

    ![Abre a página Adicionar atribuição de função para atribuir uma função à conta](./media/tutorial-discover-vmware/assign-role.png)

1. Para registrar o dispositivo, sua conta do Azure precisa ter **permissões para registrar aplicativos do AAD.**
1. No portal do Azure, acesse **Azure Active Directory** > **Usuários** > **Configurações de Usuário**.
1. Em **Configurações de usuário**, verifique se os usuários do Azure AD podem registrar aplicativos (definido como **Sim** por padrão).

    ![Verificar em Configurações de Usuário se os usuários podem registrar aplicativos do Active Directory](./media/tutorial-discover-vmware/register-apps.png)

9. Caso as configurações de 'Registros de aplicativo' estejam definidas como 'Não', solicite ao administrador global/de locatários a atribuição da permissão necessária. Como alternativa, o administrador global/de locatários pode atribuir a função **Desenvolvedor de aplicativos** a uma conta para permitir o registro do Aplicativo do AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Preparar o VMware

No vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA. Isso é necessário quando você implanta o dispositivo das Migrações para Azure como uma VM do VMware usando um arquivo OVA.

A Avaliação de Servidor precisa ter uma conta somente leitura do vCenter Server para descoberta e avaliação de VMs do VMware. Se você também quiser descobrir os aplicativos instalados e as dependências de VM, a conta precisará ter privilégios habilitados para **Máquinas Virtuais > Operações de Convidado**.

### <a name="create-an-account-to-access-vcenter"></a>Criar uma conta para acessar o vCenter

No Cliente Web vSphere, configure uma conta da seguinte maneira:

1. No Cliente Web vSphere > selecione **Administração** usando uma conta com privilégios de administrador.
2. Em **Acesso**, selecione **Usuários e Grupos de SSO**.
3. Em **Usuários**, adicione um novo usuário.
4. Em **Novo Usuário**, digite os detalhes da conta. Em seguida, clique em **OK**.
5. Em **Permissões Globais**, selecione a conta de usuário e atribua a função **Somente Leitura** à conta. Em seguida, clique em **OK**.
6. Se você também quiser descobrir os aplicativos instalados e as dependências de VM, acesse **Funções** > selecione a função **Somente leitura** e, em **Privilégios**, escolha **Operações de Convidado**. Propague os privilégios para todos os objetos no vCenter Server marcando a caixa de seleção "Propagar para filhos".
 
    ![Use a caixa de seleção para permitir operações de convidado na função Somente Leitura](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Criar uma conta para acessar as VMs

Você precisará ter uma conta de usuário com os privilégios necessários nas VMs para descobrir os aplicativos instalados e as dependências de VM. Você pode fornecer a conta de usuário no gerenciador de configurações do dispositivo. O dispositivo não instala nenhum agente nas VMs.

1. Para VMs do Windows, crie uma conta (local ou de domínio) com permissões administrativas nas VMs.
2. Para VMs do Linux, crie uma conta com privilégios de Raiz. Como alternativa, você pode criar uma conta com estas permissões nos arquivos /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

> [!NOTE]
> Atualmente, as Migrações para Azure dão suporte a uma conta de usuário para VMs do Windows e uma conta de usuário para VMs do Linux, que podem ser fornecidas no dispositivo para a descoberta de aplicativos instalados e dependências de VM.


## <a name="set-up-a-project"></a>Configurar um projeto

Configure um novo projeto das Migrações para Azure.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão geral**, selecione **Criar projeto**.
5. Em **Criar projeto**, selecione sua assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caixas para nome e região do projeto](./media/tutorial-discover-vmware/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado. A ferramenta **Migrações para Azure: Avaliação de Servidor** é adicionada por padrão ao novo projeto.

![Página mostrando a ferramenta de Avaliação de Servidor adicionada por padrão](./media/tutorial-discover-vmware/added-tool.png)

> [!NOTE]
> Se você já tiver criado um projeto, use o mesmo projeto para registrar dispositivos adicionais a fim de descobrir e avaliar um número maior de VMs.[Saiba mais](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurar o dispositivo

Migrações para Azure: Avaliação de Servidor usa um dispositivo leve de Migrações para Azure. O dispositivo executa a descoberta de VMs e envia os metadados de configuração e desempenho das VMs às Migrações para Azure. Ele pode ser configurado pela implantação de um modelo OVA que pode ser baixado do projeto de Migrações para Azure.

> [!NOTE]
> Se, por alguma razão, você não conseguir configurar o dispositivo usando o modelo, configure-o usando um script do PowerShell em um servidor Windows Server 2016 existente. [Saiba mais](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Implantar usando um modelo OVA

Para configurar o dispositivo usando um modelo OVA, siga as etapas abaixo:
1. Forneça um nome de dispositivo e gere uma chave de projeto das Migrações para Azure no portal
1. Baixe um arquivo de modelo OVA e importe-o para o vCenter Server. Verifique se o OVA é seguro.
1. Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
1. Configure o dispositivo pela primeira vez e registre-o no projeto das Migrações para Azure usando a chave de projeto das Migrações para Azure.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Gerar a chave do projeto das Migrações para Azure

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores** > **As suas máquinas são virtualizadas?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3. Em **1: Gerar chave de projeto das Migrações para Azure**, forneça um nome para o dispositivo das Migrações para Azure que você vai configurar para a descoberta das VMs VMware. O nome deverá conter até 14 caracteres alfanuméricos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página Descobrir computadores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto das Migrações para Azure** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="2-download-the-ova-template"></a>2. Baixar o modelo OVA

Em **2: Baixar o dispositivo das Migrações para Azure**, selecione o arquivo .OVA e clique em **Baixar**.

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro antes de implantá-lo:

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
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

### <a name="3-create-the-appliance-vm"></a>3. Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.
2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou o cluster no qual a VM será executada.
6. Em **Armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
8. Em **Mapeamento de Rede**, especifique a rede à qual a VM se conectará. A rede precisa ter conectividade com a Internet para enviar metadados para a Avaliação de Servidor das Migrações para Azure.
9. Revise e confirme as configurações e clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).


### <a name="4-configure-the-appliance"></a>4. Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do OVA baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No console do cliente do vSphere, clique com o botão direito do mouse na VM e selecione **Abrir console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo selecionando o atalho do aplicativo.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
   - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
     - Clique em **Configurar proxy** para especificar o endereço de proxy (na forma http://ProxyIPAddress ou http://ProxyFQDN) e na porta de escuta).
     - Especifique as credenciais caso o proxy exija autenticação.
     - Há suporte apenas para o proxy HTTP.
     - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
   - **Sincronização do horário**: O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
   - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas. Após a conclusão da verificação, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.
   - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6.7 do VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo, conforme fornecido nas **Instruções de instalação**.

     A Migração de Servidor de Migrações para Azure usa o VDDK para replicar computadores durante a migração para o Azure. 
1. Se desejar, você poderá **executar os pré-requisitos novamente** a qualquer momento durante a configuração do dispositivo para verificar se ele atende a todos os pré-requisitos.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto das Migrações para Azure** copiada do portal. Se você não tiver a chave, acesse **Avaliação do Servidor> Descobrir> Gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração da chave e copie a chave correspondente.
1. Será necessário um código do dispositivo para a autenticação com o Azure. Clicar em **Logon** abrirá uma janela restrita com o código do dispositivo, conforme mostrado abaixo.

    ![Janela restrita mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique em **Copiar código e Fazer logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, cole o código do dispositivo e entre usando seu nome de usuário e sua senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Caso feche a guia de logon por engano sem fazer logon, você precisará atualizar a guia do navegador do gerenciador de configuração do dispositivo para habilitar novamente o botão Logon.
1. Depois de fazer logon com êxito, volte para a guia anterior usando o gerenciador de configuração do dispositivo.
1. Se a conta de usuário do Azure usada para o registro em log tiver as permissões corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.



## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

1. Na **Etapa 1: Fornecer as credenciais do vCenter Server**, clique em **Adicionar credenciais** para especificar um nome amigável para as credenciais, adicionar o **Nome de usuário** e a **Senha** para a conta do vCenter Server que o dispositivo usará para descobrir as VMs na instância do vCenter Server.
    - Você deve ter configurado uma conta com as permissões necessárias no tutorial anterior.
    - Se você quiser definir o escopo da descoberta para objetos específicos do VMware (data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais do vCenter Server), examine as instruções [neste artigo](set-discovery-scope.md) para restringir a conta usada por Migrações para Azure.
1. Na **Etapa 2: Fornecer detalhes do vCenter Server**, clique em **Adicionar origem da descoberta** para selecionar o nome amigável de credenciais na lista suspensa e especifique o **Endereço IP/FQDN** da instância do vCenter Server. Você pode deixar a **Porta** como padrão (443) ou especificar uma porta personalizada na qual o vCenter Server escuta e clique em **Salvar**.
1. Quando você clicar em Salvar, o dispositivo tentará validar a conexão com o vCenter Server com as credenciais fornecidas e mostrará o **Status de validação** na tabela com relação ao endereço IP/FQDN do vCenter Server.
1. Você pode **revalidar** a conectividade com o vCenter Server a qualquer momento antes de iniciar a descoberta.
1. Na **Etapa 3: Fornecer as credenciais da VM para descobrir os aplicativos instalados e executar o mapeamento de dependência sem agente**, clique em **Adicionar credenciais** e especifique o sistema operacional para o qual as credenciais são fornecidas, o nome amigável para as credenciais e o **Nome de usuário** e a **Senha**. Em seguida, clique em **Salvar**.

    - Opcionalmente, adicione credenciais aqui se você tiver criado uma conta para uso na [descoberta de aplicativos](how-to-discover-applications.md) ou na [análise de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se você não desejar usar esses recursos, poderá clicar no controle deslizante para ignorar a etapa. Você poderá reverter a intenção a qualquer momento posterior.
    - Examine as permissões necessárias na conta para a [descoberta de aplicativos](migrate-support-matrix-vmware.md#application-discovery-requirements) ou a [análise de dependência sem agente](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Clique em **Iniciar descoberta** para iniciar a descoberta da VM. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta com relação ao endereço IP/FQDN do vCenter Server na tabela.

A descoberta funciona da seguinte maneira:
- São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal.
- A descoberta de aplicativos, funções e recursos instalados leva algum tempo. A duração depende do número de VMs sendo descobertas. Para as VMs 500, leva aproximadamente uma hora para o inventário de aplicativos aparecer no portal de Migrações para Azure.
- Depois que a descoberta de VMs for concluída, habilite a análise de dependência sem agente nas VMs desejadas pelo portal.


## <a name="next-steps"></a>Próximas etapas

- [Avaliar VMs de VMware](./tutorial-assess-vmware-azure-vm.md) a fim de obter a migração para VMs do Azure.
- [Examinar os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta.
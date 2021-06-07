---
title: 'Descobrir servidores no Hyper-V com a ferramenta Migrações para Azure: descoberta e avaliação'
description: 'Saiba como descobrir servidores locais no Hyper-V com a ferramenta Migrações para Azure: descoberta e avaliação.'
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: f461778f988fafeacc480e100b00be7d4c165dfb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612510"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Descobrir servidores em execução no Hyper-V com a ferramenta Migrações para Azure: descoberta e avaliação

Como parte de sua jornada de migração para o Azure, descubra seu inventário local e suas cargas de trabalho.

Este tutorial mostra como descobrir servidores locais em hosts do Hyper-V com a ferramenta Migrações para Azure: descoberta e avaliação usando um dispositivo leve de Migrações para Azure. O dispositivo é implantado como um servidor em um host do Hyper-V para descobrir continuamente os metadados de desempenho e do computador.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma conta do Azure
> * Preparar o ambiente do Hyper-V para descoberta.
> * Criar um projeto.
> * Configurar o dispositivo das Migrações para Azure.
> * Iniciar a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usar as opções padrão.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se estes pré-requisitos estão em vigor.

**Requisito** | **Detalhes**
--- | ---
**Host do Hyper-V** | Os hosts do Hyper-V nos quais os servidores estão localizados podem ser autônomos ou estar em um cluster.<br/><br/> O host deve executar Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/><br/> Verifique se as conexões de entrada são permitidas na porta 5985 (HTTP) do WinRM, de modo que o dispositivo possa se conectar para efetuar pull dos metadados do servidor e dos dados de desempenho usando uma sessão do modelo CIM.
**Implantação do dispositivo** | O host do Hyper-V precisa de recursos para alocar um servidor ao dispositivo:<br/><br/> – 16 GB de RAM, 8 vCPUs e cerca de 80 GB de armazenamento em disco.<br/><br/> – Um comutador virtual externo e acesso à Internet no dispositivo, diretamente ou por um proxy.
**Servidores** | Os servidores podem executar qualquer sistema operacional Windows ou Linux.

## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de usuário do Azure

Para criar um projeto e registrar o dispositivo de Migrações para Azure, você precisará ter uma conta com:
- Permissões de Colaborador ou Proprietário em uma assinatura do Azure.
- Permissões para registrar aplicativos do AAD (Azure Active Directory).

Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura. Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir as permissões da seguinte maneira:

1. No portal do Azure, pesquise por "assinaturas" e, em **Serviços**, selecione **Assinaturas**.

    ![Caixa de pesquisa para pesquisar a assinatura do Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na página **Assinaturas**, selecione a assinatura na qual deseja criar um projeto.
3. Na assinatura, selecione **Controle de acesso (IAM)**  > **Verificar o acesso**.
4. Em **Verificar o acesso**, procure a conta de usuário relevante.
5. Em **Adicionar uma atribuição de função**, clique em **Adicionar**.

    ![Pesquisar uma conta de usuário para verificar o acesso e atribuir uma função](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. Em **Adicionar atribuição de função**, selecione a função Colaborador ou Proprietário e selecione a conta (azmigrateuser em nosso exemplo). Em seguida, clique em **Salvar**.

    ![Abre a página Adicionar atribuição de função para atribuir uma função à conta](./media/tutorial-discover-hyper-v/assign-role.png)

1. Para registrar o dispositivo, sua conta do Azure precisa ter **permissões para registrar aplicativos do AAD.**
1. No portal do Azure, acesse **Azure Active Directory** > **Usuários** > **Configurações de Usuário**.
1. Em **Configurações de usuário**, verifique se os usuários do Azure AD podem registrar aplicativos (definido como **Sim** por padrão).

    ![Verificar nas Configurações de Usuário se os usuários podem registrar aplicativos do Active Directory](./media/tutorial-discover-hyper-v/register-apps.png)

9. Caso as configurações de 'Registros de aplicativo' estejam definidas como 'Não', solicite ao administrador global/de locatários a atribuição da permissão necessária. Como alternativa, o administrador global/de locatários pode atribuir a função **Desenvolvedor de aplicativos** a uma conta para permitir o registro do Aplicativo do AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Prepare os hosts do Hyper-V

Você pode preparar os hosts do Hyper-V manualmente ou usando um script. As etapas de preparação são resumidas na tabela. O script os prepara automaticamente.

**Step** | **Script** | **Manual**
--- | --- | ---
Verificar os requisitos do host | Verifica se o host está executando uma versão compatível do Hyper-V e a função do Hyper-V.<br/><br/>Habilita o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no host (necessárias para a coleta de metadados). | O host deve executar Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/><br/> Verifique se as conexões de entrada são permitidas na porta 5985 (HTTP) do WinRM, de modo que o dispositivo possa se conectar para efetuar pull dos metadados do servidor e dos dados de desempenho usando uma sessão do modelo CIM.<br/><br/> No momento, não há suporte para o script em hosts com uma localidade com idioma diferente do inglês.  
Verificar a versão do PowerShell | Verifica se você está executando o script em uma versão do PowerShell compatível. | Verifique se você está executando o PowerShell versão 4.0 ou posterior no host Hyper-V.
Criar uma conta | Verifica se você tem as permissões corretas no host do Hyper-V.<br/><br/> Permite que você crie uma conta de usuário local com as permissões corretas. | Opção 1: prepare uma conta com acesso de Administrador no computador host do Hyper-V.<br/><br/> Opção 2: prepare uma conta de Administrador Local ou de Administrador de Domínio e adicione-a a estes grupos: Usuários de Gerenciamento Remoto, Administradores do Hyper-V e Usuários do Monitor de Desempenho.
Habilitar a Comunicação Remota do PowerShell | Habilita a comunicação remota do PowerShell no host, para que o dispositivo de Migrações para Azure possa executar comandos do PowerShell no host em uma conexão do WinRM. | Para a configuração, em cada host, abra um console do PowerShell como administrador e execute este comando: ``` powershell Enable-PSRemoting -force ```
Configurar os serviços de integração do Hyper-V | Verifica se os serviços de integração do Hyper-V estão habilitados em todos os servidores gerenciados pelo host. | [Habilite os serviços de integração do Hyper-V](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada servidor.<br/><br/> Se você estiver executando o Windows Server 2003, [siga estas instruções](prepare-windows-server-2003-migration.md).
Delegar credenciais se os discos de servidor estiverem localizados em compartilhamentos SMB remotos | Delega credenciais | Execute este comando para permitir que o CredSSP delegue credenciais em hosts que executam servidores com discos em compartilhamentos SMB: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Você pode executar esse comando remotamente em todos os hosts do Hyper-V.<br/><br/> Se você adicionar novos nós de host em um cluster, eles serão adicionados automaticamente para descoberta, mas você precisará habilitar o CredSSP manualmente.<br/><br/> Ao configurar o dispositivo, você conclui a configuração do CredSSP [habilitando-o no dispositivo](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Executar o script

1. Baixe o script do [Centro de Download da Microsoft](https://aka.ms/migrate/script/hyperv). O script é assinado criptograficamente pela Microsoft.
2. Valide a integridade do script usando arquivos de hash MD5 ou SHA256. Os valores de hashtag são indicados abaixo. Execute o seguinte comando para gerar o hash para o script:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exemplo de uso:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Após validar a integridade do script, execute o script em cada host Hyper-V com este comando do PowerShell com permissões elevadas:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Os valores de hash são:

**Hash** |  **Valor**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Configurar um projeto

Configure um novo projeto.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão geral**, selecione **Criar projeto**.
5. Em **Criar projeto**, selecione sua assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caixas para nome e região do projeto](./media/tutorial-discover-hyper-v/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos até que o projeto seja implantado. A ferramenta **Migrações para Azure: descoberta e avaliação** é adicionada por padrão ao novo projeto.

![Página que mostra a ferramenta Migrações para Azure: descoberta e avaliação adicionada por padrão](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Se você já tiver criado um projeto, use o mesmo projeto para registrar dispositivos adicionais a fim de descobrir e avaliar um número maior de servidores.[Saiba mais](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurar o dispositivo

As Migrações para Azure usam um dispositivo leve das Migrações para Azure. O dispositivo executa a descoberta de servidores e envia os metadados de configuração e desempenho dos servidores às Migrações para Azure. Ele pode ser configurado pela implantação de um arquivo VHD que pode ser baixado do projeto.

> [!NOTE]
> Se, por alguma razão, você não conseguir configurar o dispositivo usando o modelo, configure-o usando um script do PowerShell em um servidor Windows Server 2016 existente. [Saiba mais](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).

Use este tutorial para configurar o dispositivo em um servidor que executa o ambiente do Hyper-V da seguinte maneira:

1. Forneça um nome de dispositivo e gere uma chave do projeto no portal.
1. Baixe um VHD compactado do Hyper-V no portal do Azure.
1. Crie o dispositivo e verifica se ele pode se conectar à Migrações para Azure: descoberta e avaliação.
1. Configure o dispositivo pela primeira vez e registre-o no projeto usando a chave do projeto.

### <a name="1-generate-the-project-key"></a>1. Gerar a chave do projeto

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: descoberta e avaliação**, selecione **Descobrir**.
2. Em **Descobrir Servidores** > **Os servidores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3. Em **1: Gerar chave do projeto**, forneça um nome para o dispositivo de Migrações para Azure que você vai configurar para a descoberta de servidores. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página Descobrir servidor durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave do projeto** será gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="2-download-the-vhd"></a>2. Baixar o VHD

Em **2: Baixar o dispositivo das Migrações para Azure**, selecione o arquivo .VHD e clique em **Baixar**.

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.

2. Execute o comando do PowerShell a seguir para gerar o hash para o arquivo zip
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Verifique as versões mais recentes do dispositivo e os valores de hash:

    - Para a nuvem pública do Azure:

        **Cenário** | **Download** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - Para o Azure Government:

        **Cenário** _ | _ *Download** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3. Criar um dispositivo

Importe o arquivo baixado e crie um dispositivo.

1. Extraia o arquivo VHD compactado em uma pasta no host do Hyper-V que hospedará o dispositivo. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Em **Ações**, clique em **Importar Máquina Virtual**.
2. No Assistente para Importar Máquina Virtual > **Antes de começar**, clique em **Próximo**.
3. Em **Localizar Pasta**, especifique a pasta que contém o VHD extraído. Em seguida, clique em **Próximo**.
1. Em **Selecionar Máquina Virtual**, clique em **Próximo**.
2. Em **Escolher Tipo de Importação**, clique em **Copiar a máquina virtual (criar uma nova ID exclusiva)** . Em seguida, clique em **Próximo**.
3. Em **Escolher Destino**, mantenha a configuração padrão. Clique em **Próximo**.
4. Em **Pastas de Armazenamento**, mantenha a configuração padrão. Clique em **Próximo**.
5. Em **Escolher Rede**, especifique o comutador virtual que será usado pelo dispositivo. O comutador precisa de conectividade com a Internet para enviar dados ao Azure.
6. Em **Resumo**, examine as configurações. Em seguida, clique em **Concluir**.
7. No Gerenciador do Hyper-V, > **Máquinas Virtuais**, inicie o dispositivo.

### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se o dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do VHD baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No Gerenciador do Hyper-V > **Máquinas Virtuais**, clique com o botão direito do mouse no dispositivo > **Conectar**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar ao dispositivo e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Conectividade**: o aplicativo verifica se o dispositivo tem acesso à Internet. Se o dispositivo usar um proxy:
      - Clique em **Configurar proxy** e especifique o endereço do proxy (no formato http://ProxyIPAddress ou http://ProxyFQDN) ) e a porta de escuta.
      - Especifique as credenciais caso o proxy exija autenticação.
      - Há suporte apenas para o proxy HTTP.
      - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
    - **Sincronização do horário**: o horário é verificado. O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta do servidor funcione corretamente.
    - **Instalar as atualizações**: a ferramenta Migrações para Azure: descoberta e avaliação verifica se o dispositivo tem as atualizações mais recentes instaladas. Após a conclusão da verificação, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto** copiada do portal. Se você não tiver a chave, acesse **Migrações para Azure: descoberta e avaliação > Descobrir > Gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração da chave e copie a chave correspondente.
1. Será necessário um código do dispositivo para a autenticação com o Azure. Clicar em **Logon** abrirá uma janela restrita com o código do dispositivo, conforme mostrado abaixo.

    ![Janela restrita mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique em **Copiar código e Fazer logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, cole o código do dispositivo e conecte-se usando seu nome de usuário do Azure e a senha.
   
   Não há suporte para a entrada com um PIN.
3. Caso feche a guia de logon por engano sem fazer logon, você precisará atualizar a guia do navegador do gerenciador de configuração do dispositivo para habilitar novamente o botão Logon.
1. Depois de fazer logon com êxito, volte para a guia anterior usando o gerenciador de configuração do dispositivo.
4. Se a conta de usuário do Azure usada para o registro em log tiver as permissões corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.

### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs de SMB

Se estiver executando VHDs em SMBs, você precisará habilitar a delegação de credenciais do dispositivo para os hosts do Hyper-V. Para fazer isso no dispositivo:

1. No dispositivo, execute este comando. HyperVHost1/HyperVHost2 são nomes de host de exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Como alternativa, faça isso no Editor de Política de Grupo Local no dispositivo:
    - Em **Política do Computador local** > **Configuração do Computador**, clique em **Modelos Administrativos** > **Sistemas** > **Delegação de Credenciais**.
    - Clique duas vezes em **Permitir delegação de novas credenciais** e selecione **Habilitado**.
    - Em **Opções**, clique em **Mostrar** e adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .
    - Em **Delegação de Credenciais**, clique duas vezes em **Permitir delegação de novas credenciais com autenticação de servidor somente NTLM**. Mais uma vez, adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Conecte-se do dispositivo a clusters ou hosts do Hyper-V e inicie a descoberta de servidor.

1. Na **Etapa 1: Fornecer as credenciais do host do Hyper-V**, clique em **Adicionar credenciais** para especificar um nome amigável para as credenciais, adicione o **Nome de usuário** e a **Senha** de um cluster/host do Hyper-V que será usado pelo dispositivo para descobrir servidores. Clique em **Save**.
1. Se desejar adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para salvar e adicionar mais credenciais. Há suporte para várias credenciais na descoberta de servidores no ambiente do Hyper-V.
1. Na **Etapa 2: Fornecer detalhes do host/cluster Hyper-V**, clique em **Adicionar origem da descoberta** para especificar o **endereço IP/FQDN** do host/cluster Hyper-V e o nome amigável para as credenciais se conectarem ao host/cluster.
1. Você pode **Adicionar um item** de cada vez ou **Adicionar vários itens** em um só lugar. Também há uma opção de fornecer detalhes do host/cluster Hyper-V por meio de **Importar CSV**.

    - Se você escolher **Adicionar um item**, precisará especificar o nome amigável para as credenciais e o **endereço IP/FQDN** do host/cluster Hyper-V e clicar em **Salvar**.
    - Se você escolher **Adicionar vários itens** _(selecionado por padrão)_ , adicione vários registros de uma vez especificando o **endereço IP/FQDN** do cluster/host do Hyper-V com o nome amigável para as credenciais na caixa de texto. **Verifique** os registros adicionados e clique em **Salvar**.
    - Se você escolher **Importar CSV**, poderá baixar um arquivo de modelo CSV, preenchê-lo com o **endereço IP/FQDN** do host/cluster Hyper-V e o nome amigável para as credenciais. Em seguida, importe o arquivo para o dispositivo, **verifique** os registros no arquivo e clique em **Salvar**.

1. Quando você clicar em Salvar, o dispositivo tentará validar a conexão com os hosts/clusters Hyper-V adicionados e mostrará o **Status de validação** na tabela em cada host/cluster.
    - Para ter hosts/clusters validados com êxito, você pode ver mais detalhes clicando no endereço IP/FQDN deles.
    - Se a validação falhar para um host, examine o erro clicando em **Falha na validação** na coluna Status da tabela. Corrija o problema e valide novamente.
    - Para remover hosts ou clusters, clique em **Excluir**.
    - Não é possível remover um host específico de um cluster. Você só pode remover o cluster inteiro.
    - Você pode adicionar um cluster, mesmo que haja problemas com hosts específicos nele.
1. Você pode **revalidar** a conectividade com os hosts/os clusters a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar descoberta** para iniciar a descoberta de servidor nos hosts/clusters validados com sucesso. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta em cada host/cluster na tabela.

Isso iniciará a descoberta. São necessários aproximadamente 2 minutos por host para que os metadados dos servidores descobertos sejam exibidos no portal do Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a conclusão da descoberta, você poderá verificar se os servidores são exibidos no portal.

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: descoberta e avaliação**, clique no ícone que exibe a contagem de **Servidores descobertos**.

## <a name="next-steps"></a>Próximas etapas

- [Avaliar servidores no ambiente do Hyper-V](tutorial-assess-hyper-v.md) quanto à migração para VMs do Azure.
- [Examinar os dados](migrate-appliance.md#collected-data---hyper-v) que o dispositivo coleta durante a descoberta.
---
title: 'Descobrir instâncias da AWS com a ferramenta Migrações para Azure: descoberta e avaliação'
description: 'Saiba como descobrir instâncias da AWS com a ferramenta Migrações para Azure: descoberta e avaliação.'
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 295cd5a6831cb64d146bb92bca74b82ff7ab29df
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771474"
---
# <a name="tutorial-discover-aws-instances-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Descobrir instâncias da AWS com a ferramenta Migrações para Azure: descoberta e avaliação

Como parte de sua jornada de migração para o Azure, descubra servidores de avaliação e migração.

Este tutorial mostra como descobrir instâncias da AWS (Amazon Web Services) com a ferramenta Migrações para Azure: descoberta e avaliação usando um dispositivo leve de Migrações para Azure. Implante o dispositivo como um servidor físico para descobrir continuamente os metadados de desempenho e do computador.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma conta do Azure.
> * Preparar instâncias da AWS para descoberta.
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
**Dispositivo** | Será necessário obter uma VM do EC2 na qual você executará o dispositivo das Migrações para Azure. O computador deverá ter:<br/><br/> – Windows Server 2016 instalado.<br/> _Não há suporte para a execução do dispositivo em um computador com o Windows Server 2019_.<br/><br/> – 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/><br/> – Um endereço IP estático ou dinâmico com acesso à Internet, de modo direto ou por meio de um proxy.
**Instâncias do Windows** | Permite obter conexões de entrada na porta 5985 (HTTP) do WinRM para que o dispositivo possa extrair metadados de configuração e desempenho.
**Instâncias do Linux** | Permite obter conexões de entrada na porta 22 (TCP).<br/><br/> As instâncias deverão usar `bash` como o shell padrão. Caso contrário, haverá falha na descoberta.

## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de usuário do Azure

Para criar um projeto e registrar o dispositivo de Migrações para Azure, você precisará ter uma conta com:

* Permissões de Colaborador ou Proprietário em uma assinatura do Azure.
* Permissões para registrar aplicativos do AAD (Azure Active Directory).

Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura. Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir as permissões da seguinte maneira:

1. No portal do Azure, pesquise por "assinaturas" e, em **Serviços**, selecione **Assinaturas**.

    ![Caixa de pesquisa para pesquisar a assinatura do Azure](./media/tutorial-discover-aws/search-subscription.png)

2. Na página **Assinaturas**, selecione a assinatura na qual deseja criar um projeto.
3. Na assinatura, selecione **Controle de acesso (IAM)**  > **Verificar o acesso**.
4. Em **Verificar o acesso**, procure a conta de usuário relevante.
5. Em **Adicionar uma atribuição de função**, clique em **Adicionar**.

    ![Pesquisar uma conta de usuário para verificar o acesso e atribuir uma função](./media/tutorial-discover-aws/azure-account-access.png)

6. Em **Adicionar atribuição de função**, selecione a função Colaborador ou Proprietário e selecione a conta (azmigrateuser em nosso exemplo). Em seguida, clique em **Salvar**.

    ![Abre a página Adicionar atribuição de função para atribuir uma função à conta](./media/tutorial-discover-aws/assign-role.png)

1. Para registrar o dispositivo, sua conta do Azure precisa ter **permissões para registrar aplicativos do AAD.**
1. No portal do Azure, acesse **Azure Active Directory** > **Usuários** > **Configurações de Usuário**.
1. Em **Configurações de usuário**, verifique se os usuários do Azure AD podem registrar aplicativos (definido como **Sim** por padrão).

    ![Verificar nas Configurações de Usuário se os usuários podem registrar aplicativos do Active Directory](./media/tutorial-discover-aws/register-apps.png)

1. Caso as configurações de 'Registros de aplicativo' estejam definidas como 'Não', solicite ao administrador global/de locatários a atribuição da permissão necessária. Como alternativa, o administrador global/de locatários pode atribuir a função **Desenvolvedor de aplicativos** a uma conta para permitir o registro do Aplicativo do AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-aws-instances"></a>Preparar instâncias da AWS

Configure uma conta que o dispositivo possa usar para acessar as instâncias da AWS.

- Para **servidores do Windows**, configure uma conta de usuário local em todos os servidores do Windows que deseja incluir na descoberta. Adicione a conta de usuário aos seguintes grupos: – Usuários de gerenciamento remoto – Usuários do Monitor de Desempenho – Usuários de Log de Desempenho.
 - Para **servidores Linux**, você precisará ter uma conta raiz nos servidores Linux que deseja descobrir. Veja as instruções na [matriz de suporte](migrate-support-matrix-physical.md#physical-server-requirements) para obter uma alternativa.
- As Migrações para Azure usam a autenticação de senha ao descobrir instâncias da AWS. As instâncias da AWS não dão suporte para a autenticação de senha por padrão. Para descobrir a instância, habilite a autenticação de senha.
    - Para servidores Windows, permita a porta 5985 (HTTP) do WinRM. Isso autorizará as chamadas remotas do WMI.
    - Para servidores Linux:
        1. Entre em cada computador Linux.
        2. Abra o arquivo sshd_config: vi /etc/ssh/sshd_config
        3. No arquivo, localize a linha **PasswordAuthentication** e altere o valor para **yes**.
        4. Salve o arquivo e feche-o. Reinicie o serviço ssh.
    - Se estiver usando um usuário raiz para descobrir os servidores Linux, verifique se o logon raiz é permitido nos servidores.
        1. Entrar em cada computador Linux
        2. Abra o arquivo sshd_config: vi /etc/ssh/sshd_config
        3. No arquivo, localize a linha **PermitRootLogin** e altere o valor para **yes**.
        4. Salve o arquivo e feche-o. Reinicie o serviço ssh.

## <a name="set-up-a-project"></a>Configurar um projeto

Configure um novo projeto.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão geral**, selecione **Criar projeto**.
5. Em **Criar projeto**, selecione sua assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caixas para nome e região do projeto](./media/tutorial-discover-aws/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos até que o projeto seja implantado. A ferramenta **Migrações para Azure: descoberta e avaliação** é adicionada por padrão ao novo projeto.

![Página mostrando a ferramenta de Avaliação de Servidor adicionada por padrão](./media/tutorial-discover-aws/added-tool.png)

> [!NOTE]
> Se você já tiver criado um projeto, use o mesmo projeto para registrar dispositivos adicionais a fim de descobrir e avaliar um número maior de servidores.[Saiba mais](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurar o dispositivo

O dispositivo de Migrações para Azure é um dispositivo leve, usado pela ferramenta Migrações para Azure: descoberta e avaliação para fazer o seguinte:

- Descobrir servidores locais.
- Enviar metadados e dados de desempenho dos servidores descobertos para a ferramenta Migrações para Azure: descoberta e avaliação.

[Saiba mais](migrate-appliance.md) sobre o dispositivo das Migrações para Azure.

Para configurar o dispositivo:

1. Forneça um nome de dispositivo e gere uma chave do projeto no portal.
1. Baixe um arquivo compactado com o script do instalador de Migrações para Azure do portal do Azure.
1. Extraia o conteúdo do arquivo compactado. Inicie o console do PowerShell com privilégios administrativos.
1. Execute o script do PowerShell para iniciar o aplicativo Web do dispositivo.
1. Configure o dispositivo pela primeira vez e registre-o no projeto usando a chave do projeto.

### <a name="1-generate-the-project-key"></a>1. Gerar a chave do projeto

1. Em **Metas de Migração** > **Windows, Linux e SQL Server** > **Migrações para Azure: descoberta e avaliação**, selecione **Descobrir**.
2. Em **Descobrir servidores** > **Os servidores estão virtualizados?** , selecione **Físico ou outro (AWS, GCP, Xen etc.)** .
3. Em **1: Gerar chave do projeto**, forneça um nome para o dispositivo de Migrações para Azure que você vai configurar para a descoberta de servidores físicos ou virtuais. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página Descobrir servidores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave do projeto** será gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="2-download-the-installer-script"></a>2. Baixe o script do instalador.

Em **2: Baixar o dispositivo das Migrações para Azure**, clique em **Baixar**.

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso da nuvem pública: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Exemplo de uso da nuvem governamental: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Verifique as versões mais recentes do dispositivo e os valores de hash:
    - Para a nuvem pública:

        **Cenário** | **Baixar** _ | _ *Valor de hash**
        --- | --- | ---
        Físico (85 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - Para o Azure Government:

        **Cenário** | **Baixar** _ | _ *Valor de hash**
        --- | --- | ---
        Físico (85 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Executar o script de instalador de Migrações para Azure
O script do instalador faz o seguinte:

- Instala agentes e um aplicativo Web para avaliação e descoberta de servidor físico.
- Instala as funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o ISE do PowerShell.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM) com detalhes de configuração persistente para Migrações para Azure.
- Cria os seguintes arquivos sob o caminho:
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Config
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Logs

Crie o script da seguinte maneira:

1. Extraia o arquivo compactado para uma pasta no servidor que hospedará o dispositivo.  Você não deve executar o script em um computador em um dispositivo de Migrações para Azure existente.
2. Inicie o PowerShell no servidor acima com privilégio administrativo (elevado).
3. Altere o diretório do PowerShell para a pasta em que o conteúdo foi extraído do arquivo compactado baixado.
4. Execute o script chamado **AzureMigrateInstaller.ps1** executando o seguinte comando:

    - Para a nuvem pública: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Para o Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    O script iniciará o aplicativo Web do dispositivo quando ele for concluído com êxito.

Se você encontrar algum problema, poderá acessar os logs do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Carimbo de data/hora</em>.log para solucionar problemas.

### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se o dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. Abra um navegador em qualquer computador que possa se conectar ao dispositivo e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho clicando no atalho do aplicativo.
2. Aceite os **termos de licença** e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Conectividade**: O aplicativo verifica se o servidor tem acesso à Internet. Se o servidor usar um proxy:
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
4. Se a conta de usuário do Azure usada para o registro em log tiver as [permissões](./tutorial-discover-physical.md) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Agora, conecte-se do dispositivo aos servidores físicos a serem descobertos e inicie a descoberta.

1. Na **Etapa 1: Forneça credenciais para a descoberta de servidores físicos ou virtuais do Windows e Linux** e clique em **Adicionar credenciais**.
1. Para o servidor Windows, selecione o tipo de origem como **Windows Server**, especifique um nome amigável para as credenciais e adicione o nome de usuário e a senha. Clique em **Save**.
1. Se estiver usando a autenticação baseada em senha para o servidor Linux, selecione o tipo de origem como **Servidor Linux (Baseado em senha)** , especifique um nome amigável para as credenciais e adicione o nome de usuário e a senha. Clique em **Save**.
1. Se estiver usando a autenticação baseada em chave SSH para o servidor Linux, selecione o tipo de origem como **Servidor Linux (Baseado em chave SSH)** , especifique um nome amigável para as credenciais, adicione o nome de usuário e procure e selecione o arquivo de chave privada SSH. Clique em **Save**.

    * As Migrações para Azure dão suporte à chave privada SSH gerada pelo comando ssh-keygen usando os algoritmos RSA, DSA, ECDSA e ed25519.
    * No momento, as Migrações para Azure não dão suporte à chave SSH baseada em frase secreta. Use uma chave SSH sem uma frase secreta.
    * No momento, as Migrações para Azure não dão suporte ao arquivo de chave privada SSH gerado pelo PuTTY.
    * As Migrações para Azure dão suporte ao formato OpenSSH do arquivo de chave privada SSH, conforme mostrado abaixo:
    
    ![Formato de chave privada SSH com suporte](./media/tutorial-discover-physical/key-format.png)


1. Se desejar adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para salvar e adicionar mais credenciais. Há suporte para várias credenciais para descoberta de servidores físicos.
1. Na **Etapa 2: Fornecer detalhes do servidor virtual ou físico**, clique em **Adicionar origem da descoberta** para especificar o **endereço IP/FQDN** do servidor e o nome amigável para as credenciais se conectarem ao servidor.
1. Você pode **Adicionar um item** de cada vez ou **Adicionar vários itens** em um só lugar. Também há uma opção de fornecer detalhes do servidor por meio de **Importar CSV**.


    - Se você escolher **Adicionar um item**, poderá escolher o tipo de sistema operacional, especificar o nome amigável para as credenciais, adicionar **endereço IP/FQDN** do servidor e clicar em **Salvar**.
    - Se você escolher **Adicionar vários itens**, adicione vários registros de uma vez especificando o **endereço IP/FQDN** do servidor com o nome amigável para as credenciais na caixa de texto. Verifique** os registros adicionados e clique em **Salvar**.
    - Se você escolher **Importar CSV** _(selecionado por padrão)_ , poderá baixar um arquivo de modelo CSV, preencher o arquivo com o **endereço IP/FQDN** do servidor e o nome amigável para as credenciais. Em seguida, importe o arquivo para o dispositivo, **verifique** os registros no arquivo e clique em **Salvar**.

1. Quando você clicar em Salvar, o dispositivo tentará validar a conexão com os servidores adicionados e mostrará o **Status de validação** na tabela em cada servidor.
    - Se a validação falhar para um servidor, examine o erro clicando em **Falha na validação** na coluna Status da tabela. Corrija o problema e valide novamente.
    - Para remover um servidor, clique em **Excluir**.
1. Você pode **revalidar** a conectividade com os servidores a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar descoberta** para iniciar a descoberta dos servidores validados com êxito. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta em cada servidor na tabela.


Isso iniciará a descoberta. São necessários aproximadamente 2 minutos por servidor para que os metadados dos servidores descobertos sejam exibidos no portal do Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a conclusão da descoberta, você poderá verificar se os servidores são exibidos no portal.

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Windows, Linux e SQL Server** > **Migrações para Azure: descoberta e avaliação**, clique no ícone que exibe a contagem de **Servidores descobertos**.

## <a name="next-steps"></a>Próximas etapas

- [Avaliar os servidores físicos](tutorial-migrate-aws-virtual-machines.md) para migrar para as VMs do Azure.
- [Examinar os dados](migrate-appliance.md#collected-data---physical) que o dispositivo coleta durante a descoberta.

---
title: Configurar um dispositivo de migrações para Azure para Hyper-V
description: Saiba como configurar um dispositivo de migrações para Azure para avaliar e migrar servidores no Hyper-V.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 8f0e7ec2566928897d2b84357b599506520e8d95
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612017"
---
# <a name="set-up-an-appliance-for-servers-on-hyper-v"></a>Configurar um dispositivo para servidores no Hyper-V

Siga este artigo para configurar o dispositivo migrações para Azure para descoberta e avaliação de servidores no Hyper-V com a ferramenta [migrações para Azure: descoberta e avaliação](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) .

O [dispositivo de migrações para Azure](migrate-appliance.md)  é um dispositivo leve usado pelas migrações para Azure: descoberta e avaliação/migração para descobrir servidores locais no Hyper-V e enviar dados de desempenho/metadados do servidor para o Azure.

Você pode implantar o dispositivo usando dois métodos:

- Configurado em um servidor no Hyper-V usando um VHD baixado. Esse método descrito neste artigo.
- Configurado em um servidor no Hyper-V ou servidor físico com um script do instalador do PowerShell. [Esse método](deploy-appliance-script.md) deve ser usado se você não puder configurar um servidor usando um VHD ou se estiver no Azure governamental.

Depois de criar o dispositivo, você verifica se ele pode se conectar ao migrações para Azure: descoberta e avaliação, configurá-lo pela primeira vez e registrá-lo no projeto.

## <a name="appliance-deployment-vhd"></a>Implantação de dispositivo (VHD)

Para configurar o dispositivo usando um modelo VHD:

- Forneça um nome de dispositivo e gere uma chave de projeto no Portal.
- Baixe um VHD compactado do Hyper-V no portal do Azure.
- Crie o dispositivo e verifique se ele pode se conectar ao migrações para Azure: descoberta e avaliação.
- Configure o dispositivo pela primeira vez e registre-o com o projeto usando a chave do projeto.

### <a name="generate-the-project-key"></a>Gerar a chave do projeto

1. Em **metas de migração**  >  **Windows, Linux e SQL Server**  >  **Migrations Azure: descoberta e avaliação**, selecione **descobrir**.
2. Em **descobrir servidores**  >  **os servidores são virtualizados?**, selecione **Sim, com o Hyper-V**.
3. Em **1: gerar chave do projeto**, forneça um nome para o dispositivo de migrações para Azure que você irá configurar para a descoberta de servidores no Hyper-V. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página descobrir servidores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="download-the-vhd"></a>Baixar o VHD

Em **2: Baixar o dispositivo das Migrações para Azure**, selecione o arquivo .VHD e clique em **Baixar**.

   ![Seleções para descobrir servidores](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Seleções para Gerar Chave](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No servidor no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

Verifique o valor de hash mais recente comparando o resultado do comando acima com o valor documentado [aqui](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#verify-security)

## <a name="create-the-appliance"></a>Criar o dispositivo

Importe o arquivo baixado e crie um dispositivo.

1. Extraia o arquivo VHD compactado em uma pasta no host Hyper-V que hospedará o dispositivo. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Em **Ações**, clique em **Importar Máquina Virtual**.

    ![Implantar o VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No Assistente para Importar Máquina Virtual > **Antes de começar**, clique em **Próximo**.
3. Em **Localizar Pasta**, especifique a pasta que contém o VHD extraído. Em seguida, clique em **Próximo**.
1. Em **Selecionar Máquina Virtual**, clique em **Próximo**.
2. Em **Escolher Tipo de Importação**, clique em **Copiar a máquina virtual (criar uma nova ID exclusiva)** . Em seguida, clique em **Próximo**.
3. Em **Escolher Destino**, mantenha a configuração padrão. Clique em **Próximo**.
4. Em **Pastas de Armazenamento**, mantenha a configuração padrão. Clique em **Próximo**.
5. Em **escolher rede**, especifique o comutador virtual que o servidor usará. O comutador precisa de conectividade com a Internet para enviar dados ao Azure.
6. Em **Resumo**, examine as configurações. Em seguida, clique em **Concluir**.
7. No Gerenciador do Hyper-V, > **Máquinas Virtuais**, inicie a máquina virtual.

### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se o dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do VHD baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No Gerenciador do Hyper-V > **máquinas virtuais**, clique com o botão direito do mouse no servidor > **conectar**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer sistema que possa se conectar ao dispositivo e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Conectividade**: O aplicativo verifica se o servidor tem acesso à Internet. Se o servidor usar um proxy:
      - Clique em **instalar proxy** para e especifique o endereço do proxy (no formato http://ProxyIPAddress ou http://ProxyFQDN) porta de escuta.
      - Especifique as credenciais caso o proxy exija autenticação.
      - Há suporte apenas para o proxy HTTP.
      - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
    - **Sincronização do horário**: o horário é verificado. O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta do servidor funcione corretamente.
    - **Instalar atualizações**: migrações para Azure: descoberta e avaliação verifica se o dispositivo tem as atualizações mais recentes instaladas. Após a conclusão da verificação, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto** copiada do Portal. Se você não tiver a chave, vá para **migrações do Azure: descoberta e avaliação> descobrir> gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração de chave e copie a chave correspondente.
1. Será necessário um código do dispositivo para a autenticação com o Azure. Clicar em **Logon** abrirá uma janela restrita com o código do dispositivo, conforme mostrado abaixo.

    ![Janela restrita mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique em **Copiar código e Fazer logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na guia novo, Cole o código do dispositivo e entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Caso feche a guia de logon por engano sem fazer logon, você precisará atualizar a guia do navegador do gerenciador de configuração do dispositivo para habilitar novamente o botão Logon.
1. Depois de fazer logon com êxito, volte para a guia anterior usando o gerenciador de configuração do dispositivo.
4. Se a conta de usuário do Azure usada para o registro em log tiver as [permissões](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
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

Conecte-se do dispositivo para hosts ou clusters do Hyper-V e inicie a descoberta.

1. Na **etapa 1: fornecer credenciais de host do Hyper-V**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de usuário** e **senha** para um host/cluster Hyper-v que o dispositivo usará para descobrir servidores. Clique em **Save**.
1. Se desejar adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para salvar e adicionar mais credenciais. Há suporte para várias credenciais para a descoberta de servidores no Hyper-V.
1. Na **Etapa 2: Fornecer detalhes do host/cluster Hyper-V**, clique em **Adicionar origem da descoberta** para especificar o **endereço IP/FQDN** do host/cluster Hyper-V e o nome amigável para as credenciais se conectarem ao host/cluster.
1. Você pode **Adicionar um item** de cada vez ou **Adicionar vários itens** em um só lugar. Também há uma opção de fornecer detalhes do host/cluster Hyper-V por meio de **Importar CSV**.

    ![Seleções para adicionar a origem da descoberta](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Se você escolher **Adicionar um item**, precisará especificar o nome amigável para as credenciais e o **endereço IP/FQDN** do host/cluster Hyper-V e clicar em **Salvar**.
    - Se você escolher **adicionar vários itens** _(selecionados por padrão)_, poderá adicionar vários registros de uma só vez especificando o host do Hyper-V/ **endereço IP do cluster/FQDN** com o nome amigável para credenciais na caixa de texto. Verifique se * * os registros adicionados e clique em **salvar**.
    - Se você escolher **Importar CSV**, poderá baixar um arquivo de modelo CSV, preenchê-lo com o **endereço IP/FQDN** do host/cluster Hyper-V e o nome amigável para as credenciais. Em seguida, importe o arquivo para o dispositivo, **verifique** os registros no arquivo e clique em **Salvar**.

1. Quando você clicar em Salvar, o dispositivo tentará validar a conexão com os hosts/clusters Hyper-V adicionados e mostrará o **Status de validação** na tabela em cada host/cluster.
    - Para ter hosts/clusters validados com êxito, você pode ver mais detalhes clicando no endereço IP/FQDN deles.
    - Se a validação falhar para um host, examine o erro clicando em **Falha na validação** na coluna Status da tabela. Corrija o problema e valide novamente.
    - Para remover hosts ou clusters, clique em **Excluir**.
    - Não é possível remover um host específico de um cluster. Você só pode remover o cluster inteiro.
    - Você pode adicionar um cluster, mesmo que haja problemas com hosts específicos nele.
1. Você pode **revalidar** a conectividade para hosts/clusters a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar descoberta** para disparar a descoberta de servidor dos hosts/clusters validados com êxito. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta em cada host/cluster na tabela.

Isso iniciará a descoberta. São necessários aproximadamente 2 minutos por host para que os metadados dos servidores descobertos sejam exibidos no portal do Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a conclusão da descoberta, você poderá verificar se os servidores são exibidos no portal.

1. Abra o painel das Migrações para Azure.
2. Na página **migrações para Azure-Windows, Linux e SQL Servers**  >  **Migrations Azure: descoberta e avaliação** , clique no ícone que exibe a contagem de **servidores descobertos**.

## <a name="next-steps"></a>Próximas etapas

Experimente a [avaliação do Hyper-V](tutorial-assess-hyper-v.md) com as migrações para Azure: descoberta e avaliação.
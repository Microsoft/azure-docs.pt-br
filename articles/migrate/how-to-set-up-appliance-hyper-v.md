---
title: Configurar um dispositivo de migrações para Azure para Hyper-V
description: Saiba como configurar um dispositivo de migrações para Azure para avaliar e migrar VMs do Hyper-V.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 3a7486ce94b335f835a88b7f357c9c719f86d667
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704751"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurar um dispositivo para VMs do Hyper-V

Siga este artigo para configurar o dispositivo de migrações para Azure para avaliação de VMs do Hyper-V com a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

O [dispositivo de migrações para Azure](migrate-appliance.md)  é um dispositivo leve usado pelas migrações para Azure: avaliação/migração do servidor para descobrir VMs do Hyper-V locais e enviar dados de metadados/desempenho da VM para o Azure.

Você pode implantar o dispositivo usando dois métodos:

- Configurar em uma VM do Hyper-V usando um VHD baixado. Esse é o método descrito neste artigo.
- Configurar em uma VM do Hyper-V ou computador físico com um script do instalador do PowerShell. [Esse método](deploy-appliance-script.md) deve ser usado se você não puder configurar uma VM usando um VHD ou se estiver no Azure governamental.

Depois de criar o dispositivo, você verifica se é possível conectá-lo ao Migrações para Azure: Avaliação do Servidor, configurá-lo pela primeira vez e registrá-lo com o projeto de Migrações para Azure.

## <a name="appliance-deployment-vhd"></a>Implantação de dispositivo (VHD)

Para configurar o dispositivo usando um modelo VHD:

- Forneça um nome de dispositivo e gere uma chave de projeto das Migrações para Azure no portal.
- Baixe um VHD compactado do Hyper-V no portal do Azure.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o no projeto das Migrações para Azure usando a chave de projeto das Migrações para Azure.

### <a name="generate-the-azure-migrate-project-key"></a>Gerar a chave do projeto das Migrações para Azure

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3. Em **1: Gerar chave de projeto das Migrações para Azure**, forneça um nome para o dispositivo das Migrações para Azure que você vai configurar para a descoberta das VMs do Hyper-V. O nome deverá conter até 14 caracteres alfanuméricos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página Descobrir computadores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto das Migrações para Azure** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="download-the-vhd"></a>Baixar o VHD

Em **2: Baixar o dispositivo das Migrações para Azure**, selecione o arquivo .VHD e clique em **Baixar**. 

   ![Seleções para Descobrir computadores](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Seleções para Gerar Chave](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie a VM.

1. Extraia o arquivo VHD compactado para uma pasta no host Hyper-V que hospedará a VM do dispositivo. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Em **Ações**, clique em **Importar Máquina Virtual**.

    ![Implantar o VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No Assistente para Importar Máquina Virtual > **Antes de começar**, clique em **Próximo**.
3. Em **Localizar Pasta**, especifique a pasta que contém o VHD extraído. Em seguida, clique em **Próximo**.
1. Em **Selecionar Máquina Virtual**, clique em **Próximo**.
2. Em **Escolher Tipo de Importação**, clique em **Copiar a máquina virtual (criar uma nova ID exclusiva)** . Em seguida, clique em **Próximo**.
3. Em **Escolher Destino**, mantenha a configuração padrão. Clique em **Próximo**.
4. Em **Pastas de Armazenamento**, mantenha a configuração padrão. Clique em **Próximo**.
5. Em **Escolher Rede**, especifique o comutador virtual que será usado pela VM. O comutador precisa de conectividade com a Internet para enviar dados ao Azure.
6. Em **Resumo**, examine as configurações. Em seguida, clique em **Concluir**.
7. No Gerenciador do Hyper-V, > **Máquinas Virtuais**, inicie a máquina virtual.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do VHD baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No Gerenciador do Hyper-V > **Máquinas Virtuais**, clique com o botão direito do mouse na VM > **Conectar**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
      - Clique em **Configurar proxy** e especifique o endereço de proxy (na forma http://ProxyIPAddress ou http://ProxyFQDN) e na porta de escuta.
      - Especifique as credenciais caso o proxy exija autenticação.
      - Há suporte apenas para o proxy HTTP.
      - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
    - **Sincronização do horário**: o horário é verificado. o horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta da VM funcione corretamente.
    - **Instalar as atualizações**: A avaliação do servidor das Migrações para Azure verifica se o dispositivo tem as últimas atualizações instaladas. Depois que a verificação for concluída, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto das Migrações para Azure** copiada do portal. Se você não tiver a chave, acesse **Avaliação do Servidor> Descobrir> Gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração da chave e copie a chave correspondente.
1. Você precisará de um código de dispositivo para autenticar com o Azure. Clicar em **logon** abrirá uma janela restrita com o código do dispositivo, conforme mostrado abaixo.

    ![Modal mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique em **copiar código & logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na guia novo, Cole o código do dispositivo e entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Caso você feche a guia de logon acidentalmente sem fazer logon, você precisa atualizar a guia navegador do Gerenciador de configuração de dispositivo para habilitar o botão de logon novamente.
1. Depois de fazer logon com êxito, volte para a guia anterior com o Gerenciador de configuração de dispositivo.
4. Se a conta de usuário do Azure usada para o registro em log tiver as [permissões](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.



### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs de SMB

Se estiver executando VHDs em SMBs, você precisará habilitar a delegação de credenciais do dispositivo para os hosts do Hyper-V. Para fazer isso no dispositivo:

1. Na VM do dispositivo, execute este comando. HyperVHost1/HyperVHost2 são nomes de host de exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Como alternativa, faça isso no Editor de Política de Grupo Local no dispositivo:
    - Em **Política do Computador local** > **Configuração do Computador**, clique em **Modelos Administrativos** > **Sistemas** > **Delegação de Credenciais**.
    - Clique duas vezes em **Permitir delegação de novas credenciais** e selecione **Habilitado**.
    - Em **Opções**, clique em **Mostrar** e adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .
    - Em **Delegação de Credenciais**, clique duas vezes em **Permitir delegação de novas credenciais com autenticação de servidor somente NTLM**. Mais uma vez, adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Conecte-se do dispositivo a clusters ou hosts do Hyper-V e inicie a descoberta de VM.

1. Na **Etapa 1: Fornecer as credenciais do host Hyper-V**, clique em **Adicionar credenciais** para especificar um nome amigável para as credenciais, adicione o **Nome de usuário** e a **Senha** para um host/cluster Hyper-V que o dispositivo usará para descobrir VMs. Clique em **Save**.
1. Se desejar adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para salvar e adicionar mais credenciais. Há suporte a várias credenciais para a descoberta de VMs Hyper-V.
1. Na **Etapa 2: Fornecer detalhes do host/cluster Hyper-V**, clique em **Adicionar origem da descoberta** para especificar o **endereço IP/FQDN** do host/cluster Hyper-V e o nome amigável para as credenciais se conectarem ao host/cluster.
1. Você pode **Adicionar um item** de cada vez ou **Adicionar vários itens** em um só lugar. Também há uma opção de fornecer detalhes do host/cluster Hyper-V por meio de **Importar CSV**.

    ![Seleções para adicionar a origem da descoberta](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Se você escolher **Adicionar um item**, precisará especificar o nome amigável para as credenciais e o **endereço IP/FQDN** do host/cluster Hyper-V e clicar em **Salvar**.
    - Se você escolher **Adicionar vários itens** _(selecionado por padrão)_ , poderá adicionar vários registros de uma vez especificando o **endereço IP/FQDN** do host/cluster Hyper-V com o nome amigável para as credenciais na caixa de texto. **Verifique** os registros adicionados e clique em **Salvar**.
    - Se você escolher **Importar CSV**, poderá baixar um arquivo de modelo CSV, preenchê-lo com o **endereço IP/FQDN** do host/cluster Hyper-V e o nome amigável para as credenciais. Em seguida, importe o arquivo para o dispositivo, **verifique** os registros no arquivo e clique em **Salvar**.

1. Quando você clicar em Salvar, o dispositivo tentará validar a conexão com os hosts/clusters Hyper-V adicionados e mostrará o **Status de validação** na tabela em cada host/cluster.
    - Para ter hosts/clusters validados com êxito, você pode ver mais detalhes clicando no endereço IP/FQDN deles.
    - Se a validação falhar para um host, examine o erro clicando em **Falha na validação** na coluna Status da tabela. Corrija o problema e valide novamente.
    - Para remover hosts ou clusters, clique em **Excluir**.
    - Não é possível remover um host específico de um cluster. Você só pode remover o cluster inteiro.
    - Você pode adicionar um cluster, mesmo que haja problemas com hosts específicos nele.
1. Você pode **revalidar** a conectividade com os hosts/clusters a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar descoberta** para iniciar a descoberta da VM dos hosts/clusters validados com êxito. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta em cada host/cluster na tabela.

Isso iniciará a descoberta. São necessários aproximadamente 2 minutos por host para que os metadados dos servidores descobertos sejam exibidos no portal do Azure.

## <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a descoberta terminar, você poderá verificar se as VMs são exibidas no portal.

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique no ícone que exibe a contagem de **Servidores descobertos**.


## <a name="next-steps"></a>Próximas etapas

Experimente a [avaliação do Hyper-V](tutorial-assess-hyper-v.md) com a avaliação do servidor de migrações para Azure.
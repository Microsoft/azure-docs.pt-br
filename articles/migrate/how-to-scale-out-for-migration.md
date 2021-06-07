---
title: Configurar um dispositivo de expansão de migrações para Azure para migração de VMware sem agente
description: Saiba como configurar um dispositivo de expansão para migrações para Azure para migrar VMs do Hyper-V.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 5ca821cb4f85deb77595e4a9029cc10298dbb884
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611966"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Dimensionar a migração sem agente de máquinas virtuais VMware para o Azure

Este artigo ajuda você a entender como usar um dispositivo de expansão para migrar um grande número de VMs (máquinas virtuais) VMware para o Azure usando o método sem agente da ferramenta de migração de servidor migrações para migração de VMs VMware.

Usando o método de migração sem agente para máquinas virtuais VMware, você pode:

- Replique até 300 VMs de um único servidor vCenter simultaneamente usando um dispositivo de migrações para Azure.
- Replique até 500 VMs de um único servidor vCenter simultaneamente implantando um segundo dispositivo de expansão para migração.

Neste artigo, você aprenderá a:

- Adicionar um dispositivo de expansão para a migração sem agente de máquinas virtuais VMware
- Migre até 500 VMs simultaneamente usando o dispositivo de expansão.

##  <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa executar as seguintes etapas:

- Crie o projeto de migrações para Azure.
- Implante o dispositivo de migrações para Azure (dispositivo primário) e conclua a descoberta de máquinas virtuais VMware gerenciadas por seu servidor vCenter.
- Configure a replicação para uma ou mais máquinas virtuais que devem ser migradas.
> [!IMPORTANT]
> Você precisará ter pelo menos uma máquina virtual de replicação no projeto antes de poder adicionar um dispositivo de expansão para migração.

Para saber como executar as informações acima, consulte o tutorial sobre como [migrar máquinas virtuais VMware para o Azure com o método de migração sem agente](./tutorial-migrate-vmware.md).

## <a name="deploy-a-scale-out-appliance"></a>Implantar um dispositivo de expansão

Para adicionar um dispositivo de expansão, siga as etapas mencionadas abaixo:

1. Clique em **descobrir**  >  **seus computadores são virtualizados?** 
1. Selecione **Sim, com VMware vSphere hipervisor.**
1. Selecione replicação sem agente na próxima etapa.
1. Selecione **escalar horizontalmente um dispositivo primário existente** no menu Selecionar o tipo de dispositivo.
1. Selecione o dispositivo primário (o dispositivo que usa a descoberta que foi realizada) que você deseja expandir.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Página descobrir computadores para integração horizontal":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Gerar a chave do projeto das Migrações para Azure

1. Em **gerar chave de projeto de migrações para Azure**, forneça um nome de sufixo para o dispositivo de expansão. O sufixo pode conter apenas caracteres alfanuméricos e tem um limite de tamanho de 14 caracteres.
2. Clique em **gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página de descoberta durante a criação de recursos.
3. Copie a chave gerada. Você precisará da chave mais tarde para concluir o registro do dispositivo de expansão.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. Baixe o instalador para o dispositivo de expansão

Em **baixar dispositivo de migrações para Azure**, clique em  **baixar**. Você precisa baixar o script do instalador do PowerShell para implantar o dispositivo de expansão em um servidor existente que esteja executando o Windows Server 2016 e com a configuração de hardware necessária (32-GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e acesso à Internet, diretamente ou através de um proxy).
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Baixar o script para o dispositivo de expansão":::

> [!TIP]
> Você pode validar a soma de verificação do arquivo zip baixado usando estas etapas:
>
> 1. Abrir o prompt de comando como administrador
> 2. Execute o seguinte comando para gerar o hash para o arquivo zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso da nuvem pública: ```C:\>Get-FileHash -Path .\AzureMigrateInstaller-VMware-Public-Scaleout.zip -Algorithm SHA256 ```
> 3. Baixe a versão mais recente do instalador do dispositivo de expansão no portal se o valor de hash calculado não corresponder a esta cadeia de caracteres: 1E6B6E3EE8B2A800818B925F5DA67EF7874DAD87E32847120B32F3E21F5960F9

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Executar o script de instalador de Migrações para Azure
O script do instalador faz o seguinte:

- Instala o agente de gateway e o Gerenciador de configuração de dispositivo para executar mais replicações de servidor simultâneas.
- Instala as funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o ISE do PowerShell.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM) com detalhes de configuração persistente para Migrações para Azure.
- Cria os seguintes arquivos sob o caminho:
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Config
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Logs

Crie o script da seguinte maneira:

1. Extraia o arquivo zip para uma pasta no servidor que hospedará o dispositivo de expansão.  Certifique-se de não executar o script em um servidor com um dispositivo de migrações para Azure existente.
2. Inicie o PowerShell no servidor acima com privilégio administrativo (elevado).
3. Altere o diretório do PowerShell para a pasta em que o conteúdo foi extraído do arquivo zip baixado.
4. Execute o script chamado **AzureMigrateInstaller.ps1**  usando o seguinte comando:

    - Para a nuvem pública: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    O script iniciará o Gerenciador de configuração do dispositivo quando concluir a execução.

Se você tiver problemas, poderá acessar os logs de script em: <br/> C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log para solução de problemas.


### <a name="4-configure-the-appliance"></a>4. Configurar o dispositivo

Antes de começar, verifique se [esses pontos de extremidade do Azure](migrate-appliance.md#public-cloud-urls) estão acessíveis do dispositivo de expansão.

- Abra um navegador em qualquer computador que possa se conectar ao servidor do dispositivo de escalabilidade horizontal e abra a URL do Gerenciador de configuração do dispositivo: **https://*nome do dispositivo de escala horizontal ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o Gerenciador de configurações na área de trabalho do servidor de dispositivo de escalabilidade horizontal usando o atalho para o Gerenciador de configurações.
- Aceite os **termos de licença** e leia as informações de terceiros.
- No Configuration Manager > **Configurar pré-requisitos**, siga estas etapas:
   - **Conectividade**: o dispositivo verifica se o servidor tem acesso à Internet. Se o servidor usar um proxy:
     1. Clique em **Configurar proxy** para especificar o endereço do proxy (no formulário http://ProxyIPAddress ou http://ProxyFQDN) porta de escuta.
     2. Especifique as credenciais caso o proxy exija autenticação.
     3. Há suporte apenas para o proxy HTTP.
     4. Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
   - **Sincronização do horário**: O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
   - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas. Após a conclusão da verificação, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos serviços em execução no servidor de dispositivo.
   - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6,7 do VMware e extraia o conteúdo do zip baixado para o local especificado no dispositivo, conforme fornecido nas **instruções de instalação** na tela do dispositivo Configuration Manager.


### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto das Migrações para Azure** copiada do portal. Se você não tiver a chave, vá para **avaliação do servidor> descobrir> gerenciar dispositivos existentes**, selecione o nome do dispositivo primário, localize o dispositivo de expansão associado a ele e copie a chave correspondente.
1. Será necessário um código do dispositivo para a autenticação com o Azure. Clicar em **Logon** abrirá uma janela restrita com o código do dispositivo, conforme mostrado abaixo.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Janela restrita mostrando o código do dispositivo":::

1. Clique em **Copiar código e Fazer logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na guia novo, Cole o código do dispositivo e entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Caso feche a guia de logon por engano sem fazer logon, você precisará atualizar a guia do navegador do gerenciador de configuração do dispositivo para habilitar novamente o botão Logon.
1. Depois de fazer logon com êxito, volte para a guia anterior com o Gerenciador de configuração de dispositivo.
1. Se a conta de usuário do Azure usada para o registro em log tiver as permissões corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Painel 2 no Configuration Manager do dispositivo":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Importar a configuração do dispositivo do dispositivo primário

Para concluir o registro do dispositivo de expansão, clique em **importar** para obter os arquivos de configuração necessários do dispositivo primário.

1. Clicar em **importar** abre uma janela pop-up com instruções sobre como importar os arquivos de configuração necessários do dispositivo primário.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Importar configuração modal":::
1. Faça logon (área de trabalho remota) no dispositivo primário e execute os seguintes comandos do PowerShell:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Copie o arquivo zip criado executando os comandos acima para o dispositivo de expansão. O arquivo zip contém os arquivos de configuração necessários para registrar o dispositivo de expansão.
1. Na janela pop-up aberta na etapa anterior, selecione o local do arquivo zip de configuração copiado e clique em **salvar**.

Depois que os arquivos tiverem sido importados com êxito, o registro do dispositivo de expansão será concluído e mostrará o carimbo de data/hora da última importação bem-sucedida. Você também pode ver os detalhes do registro clicando em **Exibir detalhes**.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="Captura de tela mostra o registro do dispositivo de expansão com o projeto de migrações para Azure.":::

Neste ponto, você deve revalidar que o dispositivo de expansão é capaz de se conectar ao seu servidor do vCenter. Clique em **revalidar** para validar vCenter Server conectividade do dispositivo de expansão.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Captura de tela mostra as credenciais de exibição e as fontes de descoberta a serem validadas.":::

> [!IMPORTANT]
> Se você editar as credenciais de vCenter Server no dispositivo primário, certifique-se de importar os arquivos de configuração novamente para o dispositivo de expansão para obter a configuração mais recente e continuar com as replicações em andamento.<br/> Se você não precisar mais do dispositivo de expansão, certifique-se de desabilitar o dispositivo de expansão. [**Saiba mais**](./common-questions-appliance.md) sobre como desabilitar o dispositivo de expansão quando não for necessário.

## <a name="replicate"></a>Replicar

1. Após o registro do dispositivo de expansão, no bloco migrações para Azure: migração de servidor, clique em **replicar**.

2.  Siga as etapas na tela para iniciar a replicação de mais máquinas virtuais. 

Com o dispositivo de expansão em vigor, agora você pode replicar máquinas virtuais 500 simultaneamente. Você também pode migrar VMs em lotes de 200 por meio do portal do Azure.

A ferramenta de migração de servidor de migrações para Azure cuidará da distribuição das máquinas virtuais entre o dispositivo primário e de expansão para replicação. Quando a replicação for concluída, você poderá migrar as máquinas virtuais.

> [!TIP]
> É recomendável migrar máquinas virtuais em lotes de 200 para obter um desempenho ideal se você quiser migrar um grande número de máquinas virtuais.
  
## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu que:
- Como configurar um dispositivo de expansão
- Como replicar VMs usando um dispositivo de expansão


[Saiba mais](./tutorial-migrate-vmware.md) sobre como migrar servidores para o Azure usando migrações para Azure: ferramenta de migração de servidor.
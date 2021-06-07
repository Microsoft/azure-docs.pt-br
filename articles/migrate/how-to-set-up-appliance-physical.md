---
title: Configurar um dispositivo de migrações para Azure para servidores físicos
description: Saiba como configurar um dispositivo de migrações para Azure para descoberta e avaliação de servidor físico.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 9052cbd3dc728b50b62c33f3a11a5e36a7504f29
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771559"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurar um dispositivo para servidores físicos

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando servidores físicos com a ferramenta migrações para Azure: descoberta e avaliação.

O dispositivo de migrações para Azure é um dispositivo leve, usado pelas migrações para Azure: descoberta e avaliação para fazer o seguinte:

- Descobrir servidores locais.
- Enviar dados de desempenho e metadados para servidores descobertos para migrações para Azure: descoberta e avaliação.

[Saiba mais](migrate-appliance.md) sobre o dispositivo das Migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo:

- Forneça um nome de dispositivo e gere uma chave de projeto no Portal.
- Baixe um arquivo compactado com o script do instalador de Migrações para Azure do portal do Azure.
- Extraia o conteúdo do arquivo compactado. Inicie o console do PowerShell com privilégios administrativos.
- Execute o script do PowerShell para iniciar o aplicativo Web do dispositivo.
- Configure o dispositivo pela primeira vez e registre-o com o projeto usando a chave do projeto.

### <a name="generate-the-project-key"></a>Gerar a chave do projeto

1. Em **metas de migração**  >  **Windows, Linux e SQL Server**  >  **Migrations Azure: descoberta e avaliação**, selecione **descobrir**.
2. Em **descobrir servidores**  >  **os servidores são virtualizados?**, selecione **físico ou outro (AWS, GCP, Xen, etc.)**.
3. Em **1: gerar chave do projeto**, forneça um nome para o dispositivo de migrações para Azure que você irá configurar para a descoberta de servidores físicos ou virtuais. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página descobrir servidores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="download-the-installer-script"></a>Baixe o script do instalador.

Em **2: Baixar o dispositivo das Migrações para Azure**, clique em **Baixar**.

   ![Seleções para Descobrir computadores](./media/tutorial-assess-physical/servers-discover.png)


   ![Seleções para Gerar Chave](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. Nos servidores nos quais você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso da nuvem pública: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Exemplo de uso da nuvem governamental: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Verifique a versão mais recente do dispositivo e as configurações de [valores de hash](tutorial-discover-physical.md#verify-security) .
 

## <a name="run-the-azure-migrate-installer-script"></a>Executar o script de instalador de Migrações para Azure
O script do instalador faz o seguinte:

- Instala agentes e um aplicativo Web para avaliação e descoberta de servidor físico.
- Instala as funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o ISE do PowerShell.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM) com detalhes de configuração persistente para Migrações para Azure.
- Cria os seguintes arquivos sob o caminho:
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Config
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Logs

Crie o script da seguinte maneira:

1. Extraia o arquivo compactado para uma pasta no servidor que hospedará o dispositivo.  Certifique-se de não executar o script em um servidor que tenha um dispositivo de migrações para Azure existente.
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

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. Abra um navegador em qualquer computador que possa se conectar ao dispositivo e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho clicando no atalho do aplicativo.
2. Aceite os **termos de licença** e leia as informações de terceiros.
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
4. Se a conta de usuário do Azure usada para o registro em log tiver as [permissões](./tutorial-discover-physical.md) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Agora, conecte-se do dispositivo aos servidores físicos a serem descobertos e inicie a descoberta.

1. Na **Etapa 1: Forneça credenciais para a descoberta de servidores físicos ou virtuais do Windows e Linux** e clique em **Adicionar credenciais**.
1. Para o Windows Server, selecione o tipo de origem como **Windows Server**, especifique um nome amigável para credenciais, adicione o nome de usuário e a senha. Clique em **Save**.
1. Se você estiver usando a autenticação baseada em senha para o servidor Linux, selecione o tipo de origem como **servidor Linux (baseado em senha)**, especifique um nome amigável para as credenciais, adicione o nome de usuário e a senha. Clique em **Save**.
1. Se você estiver usando a autenticação baseada em chave SSH para o servidor Linux, poderá selecionar tipo de origem como **servidor Linux (baseado em chave SSH)**, especificar um nome amigável para credenciais, adicionar o nome de usuário, procurar e selecionar o arquivo de chave privada SSH. Clique em **Save**.

    - As migrações para Azure dão suporte à chave privada SSH gerada pelo comando ssh-keygen usando algoritmos RSA, DSA, ECDSA e ed25519.
    - Atualmente, as migrações para Azure não oferecem suporte à chave SSH baseada em senha. Use uma chave SSH sem uma frase secreta.
    - No momento, as Migrações para Azure não dão suporte ao arquivo de chave privada SSH gerado pelo PuTTY.
    - As Migrações para Azure dão suporte ao formato OpenSSH do arquivo de chave privada SSH, conforme mostrado abaixo:
    
    ![Formato de chave privada SSH com suporte](./media/tutorial-discover-physical/key-format.png)
1. Se desejar adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para salvar e adicionar mais credenciais. Há suporte para várias credenciais para descoberta de servidores físicos.
1. Na **Etapa 2: Fornecer detalhes do servidor virtual ou físico**, clique em **Adicionar origem da descoberta** para especificar o **endereço IP/FQDN** do servidor e o nome amigável para as credenciais se conectarem ao servidor.
1. Você pode **Adicionar um item** de cada vez ou **Adicionar vários itens** em um só lugar. Também há uma opção de fornecer detalhes do servidor por meio de **Importar CSV**.

    ![Seleções para adicionar a origem da descoberta](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Se você escolher **Adicionar um item**, poderá escolher o tipo de sistema operacional, especificar o nome amigável para as credenciais, adicionar **endereço IP/FQDN** do servidor e clicar em **Salvar**.
    - Se você escolher **adicionar vários itens**, poderá adicionar vários registros ao mesmo tempo especificando o **endereço IP do servidor/FQDN** com o nome amigável para credenciais na caixa de texto. Verifique se * * os registros adicionados e clique em **salvar**.
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
2. Na página **migrações para Azure-Windows, Linux e SQL Servers**  >  **Migrations Azure: descoberta e avaliação** , clique no ícone que exibe a contagem de **servidores descobertos**.


## <a name="next-steps"></a>Próximas etapas

Experimente a [avaliação de servidores físicos](tutorial-assess-physical.md) com as migrações para Azure: descoberta e avaliação.
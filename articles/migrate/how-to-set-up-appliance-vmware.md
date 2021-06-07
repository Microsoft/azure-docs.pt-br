---
title: Configurar um dispositivo de migrações para Azure para VMware
description: Saiba como configurar um dispositivo de migrações para Azure para avaliar e migrar servidores no ambiente VMware.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 685d7f0a0aaab2f38967e0eb6c32c3fb4067dbe3
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612816"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>Configurar um dispositivo para servidores no ambiente VMware

Siga este artigo para configurar o dispositivo migrações para Azure para avaliação com a ferramenta [migrações para Azure: descoberta e avaliação](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) e para a migração sem agente usando a ferramenta [migrações para Azure: servidor de migração](migrate-services-overview.md#azure-migrate-server-migration-tool) .

O [dispositivo de migrações para Azure](migrate-appliance.md) é um dispositivo leve usado pelas migrações para Azure: descoberta e avaliação e migração de servidor para descobrir servidores em execução no vCenter Server, enviar metadados de configuração e desempenho do servidor para o Azure e para replicação de servidores usando a migração sem agente.

Você pode implantar o dispositivo usando dois métodos:

- Crie um servidor em vCenter Server usando um modelo OVA baixado. Esse é o método descrito neste artigo.
- Configure o dispositivo em um servidor existente usando um script do instalador do PowerShell. [Esse método](deploy-appliance-script.md) deve ser usado se você não puder usar o modelo ova ou se estiver no Azure governamental.

Depois de criar o dispositivo, você verifica se ele pode se conectar ao migrações para Azure: descoberta e avaliação, registrá-lo com o projeto e configurar o dispositivo para iniciar a descoberta.

## <a name="deploy-with-ova"></a>Implantar usando um modelo OVA

Para configurar o dispositivo usando um modelo OVA, siga as etapas abaixo:

1. Forneça um nome de dispositivo e gere uma chave de projeto no Portal.
1. Baixe um arquivo de modelo OVA e importe-o para o vCenter Server. Verifique se o OVA é seguro.
1. Crie a VM do dispositivo com base no arquivo OVA e verifique se ele pode se conectar às Migrações para Azure.
1. Configure o dispositivo pela primeira vez e registre-o com o projeto usando a chave do projeto.

### <a name="1-generate-the-project-key"></a>1. gerar a chave do projeto

1. Em **objetivos de migração**  >  **servidores**  >  **migrações para Azure: descoberta e avaliação**, selecione **descobrir**.
2. Em **descobrir servidores**  >  **os servidores são virtualizados?**, selecione **Sim, com VMware vSphere hipervisor**.
3. Em **1: gerar chave do projeto**, forneça um nome para o dispositivo de migrações para Azure que você irá configurar para a descoberta de servidores em seu ambiente VMware. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página de descoberta durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma chave de projeto * * é gerada.
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

Verifique se o servidor do dispositivo pode se conectar às URLs do Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls) .

### <a name="4-configure-the-appliance"></a>4. Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [**script do PowerShell**](deploy-appliance-script.md) em vez do OVA baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No console do cliente do vSphere, clique com o botão direito do mouse no servidor e selecione **Abrir Console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer servidor que possa se conectar ao servidor do dispositivo e abra a URL do Gerenciador de configuração do dispositivo: `https://appliance name or IP address: 44368` .

   Como alternativa, você pode abrir o Configuration Manager na área de trabalho do servidor de dispositivo selecionando o atalho para o Configuration Manager.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No Configuration Manager > **Configurar pré-requisitos**, siga estas etapas:
   - **Conectividade**: o dispositivo verifica se o servidor tem acesso à Internet. Se o servidor usar um proxy:
     - Clique em **Configurar proxy** para especificar o endereço de proxy no formulário `http://ProxyIPAddress` ou na `http://ProxyFQDN` porta de escuta.
     - Especifique as credenciais caso o proxy exija autenticação.
     - Há suporte apenas para o proxy HTTP.
     - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
   - **Sincronização do horário**: O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
   - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas. Após a conclusão da verificação, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos serviços em execução no servidor de dispositivo.
   - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6.7 do VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo, conforme fornecido nas **Instruções de instalação**.

     A Migração de Servidor das Migrações para Azure usa o VDDK para replicar servidores durante a migração para o Azure. 
1. Se desejar, você poderá **executar os pré-requisitos novamente** a qualquer momento durante a configuração do dispositivo para verificar se ele atende a todos os pré-requisitos.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Painel 1 no Configuration Manager do dispositivo":::

## <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto** copiada do Portal. Se você não tiver a chave, vá para **descoberta e avaliação> descobrir> gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração de chave e copie a chave correspondente.
1. Será necessário um código do dispositivo para a autenticação com o Azure. Clicar em **Logon** abrirá uma janela restrita com o código do dispositivo, conforme mostrado abaixo.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Janela restrita mostrando o código do dispositivo":::

1. Clique em **Copiar código e Fazer logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na guia novo, Cole o código do dispositivo e entre usando seu nome de usuário e senha do Azure.
   
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
    - Se você quiser fazer o escopo da descoberta para objetos específicos do VMware (vCenter Server data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou servidores individuais.), examine as instruções neste [artigo](set-discovery-scope.md) para restringir a conta usada pelas migrações para Azure.
1. Na **Etapa 2: Fornecer detalhes do vCenter Server**, clique em **Adicionar origem da descoberta** para selecionar o nome amigável das credenciais na lista suspensa e especifique o **Endereço IP/FQDN** do vCenter Server. Você pode deixar a **Porta** como padrão (443) ou especificar uma porta personalizada na qual o vCenter Server escuta e clique em **Salvar**.
1. Quando você clicar em **Salvar**, o dispositivo tentará validar a conexão com o vCenter Server com as credenciais fornecidas e mostrará o **Status de validação** na tabela com relação ao endereço IP/FQDN do vCenter Server.
1. Você pode **revalidar** a conectividade para vCenter Server a qualquer momento antes de iniciar a descoberta.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Painel no Gerenciador de configuração do dispositivo para obter vCenter Server detalhes":::

### <a name="provide-server-credentials"></a>Fornecer credenciais do servidor

Na **Etapa 3: Fornecer credenciais de servidor para executar o inventário de software, a análise de dependências sem agente e a descoberta de instâncias e bancos de dados do SQL Server**, você pode optar por fornecer várias credenciais de servidor ou, se não quiser aproveitar esses recursos, poderá optar por ignorar a etapa e prosseguir com a descoberta do vCenter Server. Você pode alterar sua intenção a qualquer momento mais tarde.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Painel 3 no Configuration Manager do dispositivo para obter detalhes do servidor":::


Se você quiser aproveitar esses recursos, poderá fornecer credenciais de servidor seguindo as etapas abaixo. O dispositivo tentará mapear automaticamente as credenciais para os servidores para executar os recursos de descoberta.

- Você pode adicionar credenciais do servidor clicando no botão **Adicionar credenciais** . Isso abrirá uma janela restrita na qual você pode escolher o **tipo de credenciais** na lista suspensa.
- Você pode fornecer credenciais de autenticação de domínio/Windows (não domínio)/Linux (não domínio)/SQL Server. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as manipulamos.​
- Para cada tipo de credenciais, você precisa especificar um nome amigável para credenciais, adicionar um **nome de usuário** e uma **senha** e clicar em **Salvar**.
- Se você escolher credenciais de domínio, também precisará especificar o FQDN para o domínio. O FQDN é necessário para validar a autenticidade das credenciais com a Active Directory desse domínio.
- Examine as [permissões necessárias](add-server-credentials.md#required-permissions) na conta para a descoberta de aplicativos instalados, a análise de dependências sem agente ou para a descoberta de instâncias e bancos de dados do SQL Server.
- Se desejar adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para salvar e adicionar mais credenciais.
- Quando você clica em **Salvar** ou **Adicionar mais**, o dispositivo valida as credenciais de domínio com o Active Directory do domínio para a autenticidade delas. Isso é feito para evitar bloqueios de conta quando o dispositivo executa várias iterações para mapear credenciais para os respectivos servidores.
- Você pode ver o **Status de validação** de todas as credenciais de domínio na tabela de credenciais. Somente as credenciais de domínio serão validadas.
- Se a validação falhar, você poderá clicar no status **Com falha** para ver o erro encontrado e clicar em **Revalidar credenciais** após corrigir o problema para validar as credenciais de domínio com falha novamente.
    :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Painel 3 no Gerenciador de configurações de dispositivo para fornecer várias credenciais":::

### <a name="start-discovery"></a>Iniciar descoberta

1. Clique em **Iniciar descoberta** para iniciar a descoberta do vCenter Server. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta com relação ao endereço IP/FQDN do vCenter Server na tabela de origens.
1. Se você tiver fornecido credenciais de servidor, o inventário de software (descoberta de aplicativos instalados) será iniciado automaticamente após a conclusão da descoberta do vCenter Server. O inventário de software é executado uma vez a cada 12 horas.
1. O [inventário de software](how-to-discover-applications.md) identifica as instâncias do SQL Server sendo executadas nos servidores e usando as informações, as tentativas do dispositivo de se conectar às instâncias por meio da autenticação do Windows ou das credenciais de autenticação do SQL Server fornecidas no dispositivo e coletar dados nos bancos do SQL Server e as propriedades deles. A descoberta do SQL é realizada uma vez a cada 24 horas.
1. Durante o inventário de software, as credenciais dos servidores adicionados serão iteradas em relação aos servidores e validadas para análise de dependência sem agente. Você pode habilitar a análise de dependência sem agente para servidores do Portal. Somente os servidores em que a validação é realizada com sucesso podem ser selecionados para habilitar a análise de dependências sem agente.

A descoberta funciona da seguinte maneira:

- São necessários cerca de 15 minutos para que o inventário de servidores descobertos seja exibido no portal.
- A descoberta de aplicativos instalados pode levar algum tempo. A duração depende do número de servidores descobertos. Para 500 servidores, leva aproximadamente uma hora para o inventário de aplicativos aparecer no portal das Migrações para Azure.
- Depois que a descoberta de servidores for concluída, habilite a análise de dependências sem agente nos servidores do portal.
- As instâncias e os dados de bancos de dados do SQL Server começarão a aparecer no portal dentro de 24 horas do início da descoberta.

## <a name="next-steps"></a>Próximas etapas

Examine os tutoriais para [avaliação do VMware](./tutorial-assess-vmware-azure-vm.md) e [migração sem agente](tutorial-migrate-vmware.md).

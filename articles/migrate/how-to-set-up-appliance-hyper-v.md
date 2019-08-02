---
title: Configurar um dispositivo para Azure migrar avaliação/migração do servidor para VMs do Hyper-V | Microsoft Docs
description: Descreve como configurar um dispositivo para a descoberta, avaliação e migração sem agente de VMs do Hyper-V usando o Azure migrar avaliação/migração do servidor.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811759"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurar um dispositivo para as VMs do Hyper-V

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando as VMs do Hyper-V com a ferramenta de avaliação de servidor de migrar do Azure ou migrar VMs VMware para o Azure usando a ferramenta de migração do servidor de migrar do Azure.

O dispositivo de VM do Hyper-V é um dispositivo leve usado pelo Azure migrar avaliação/migração do servidor para fazer o seguinte:

- Descobrir VMs do Hyper-V local.
- Envie dados de desempenho e de metadados para as VMs descobertas para Azure migrar avaliação/migração do servidor.

[Saiba mais](migrate-appliance.md) sobre o dispositivo de migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo de você:
- Baixe um VHD do Hyper-V compactado do portal do Azure.
- Criar o dispositivo e verifique se ele pode se conectar à avaliação de servidor de migrar do Azure. 
- Configurar o dispositivo pela primeira vez e registrá-lo com o projeto de migrações para Azure.

## <a name="download-the-vhd"></a>Baixe o VHD

Baixe o modelo compactado do VHD para o dispositivo.

1. Na **metas de migração** > **servidores** > **migrações para Azure: Avaliação de servidor**, clique em **Discover**.
2. Na **descobrir máquinas** > **os computadores são virtualizados?** , clique em **Sim, com o Hyper-V**.
3. Clique em **baixar** para baixar o arquivo VHD.

    ![Baixar a VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro, antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Para a versão do dispositivo 1.19.05.10, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>Criar o dispositivo de VM

Importe o arquivo baixado e crie a VM.

1. Extraia o arquivo compactado de VHD para uma pasta no host Hyper-V que hospedará o dispositivo de VM. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Na **ações**, clique em **importar Máquina Virtual**.

    ![Implantar um VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No Assistente de importação de máquina Virtual > **antes de começar**, clique em **próxima**.
3. Na **localizar pasta**, especifique a pasta que contém o VHD extraído. Clique em **Avançar**.
1. Na **Selecionar Máquina Virtual**, clique em **próxima**.
2. Na **Escolher tipo de importação**, clique em **copiar a máquina virtual (criar uma nova ID exclusiva)** . Clique em **Avançar**.
3. Na **Escolher destino**, deixe a configuração padrão. Clique em **Avançar**.
4. Na **pastas de armazenamento**, deixe a configuração padrão. Clique em **Avançar**.
5. Na **escolha rede**, especifique o comutador virtual que a VM usará. O switch precisa de conectividade de internet para enviar dados para o Azure.
6. Na **resumo**, examine as configurações. Em seguida, clique em **Concluir**.
7. No Gerenciador do Hyper-V > **máquinas virtuais**, inicie a máquina virtual.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso de dispositivo para o Azure

Certifique-se de que o dispositivo de VM pode se conectar ao [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. No Gerenciador do Hyper-V > **máquinas virtuais**, clique com botão direito a VM > **Connect**.
2. Forneça o idioma, fuso horário e a senha para o dispositivo.
3. Abra um navegador em qualquer computador que pode se conectar à VM e abra a URL do aplicativo web de dispositivo: **https://*nome do dispositivo ou o endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo da área de trabalho do dispositivo clicando no atalho do aplicativo.
1. No aplicativo web > **configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: Aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: O aplicativo verifica que a VM tem acesso à internet. Se a VM usa um proxy:
        - Clique em **as configurações de Proxy**e especifique o endereço de proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais caso o proxy exija autenticação.
        - Há suporte apenas para o proxy HTTP.
    - **Sincronização de tempo**: Tempo é verificado. A hora em que o dispositivo deve ser sincronia com o horário da internet para a descoberta VM funcionar corretamente.
    - **Instalar atualizações**: Avaliação de servidor de migrar do Azure verifica se o dispositivo tem as últimas atualizações instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo com as migrações para Azure

1. Clique em **faça logon no**. Se não for exibido, verifique se que você desabilitou o Bloqueador de pop-up no navegador.
2. Na nova guia, entre usando suas credenciais do Azure. 
    - Entrar com seu nome de usuário e senha.
    - Entrar com um PIN não é suportado.
3. Depois de entrar com êxito, volte para o aplicativo web.
4. Selecione a assinatura na qual o projeto de migrações para Azure foi criado. Em seguida, selecione o projeto.
5. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique em **Registrar**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs de SMB

Se você estiver executando VHDs em SMBs, você deve habilitar a delegação de credenciais do dispositivo para os hosts do Hyper-V. Para fazer isso do dispositivo:

1. No dispositivo de VM, execute este comando. HyperVHost1/HyperVHost2 são exemplos de nomes de host.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Como alternativa, faça o seguinte no Editor de diretiva de grupo de Local no dispositivo:
    - Na **política do computador Local** > **configuração do computador**, clique em **modelos administrativos** > **dosistema**  >  **Delegação de credenciais**.
    - Clique duas vezes em **permitir delegação de novas credenciais**e selecione **habilitado**.
    - Na **opções**, clique em **mostram**, e adicione cada host Hyper-V que você deseja descobrir a lista, com **wsman /** como um prefixo.
    - Na **delegação de credenciais**, clique duas vezes em **permitir delegação de novas credenciais com autenticação de servidor somente NTLM**. Adicionar mais uma vez, cada host Hyper-V que você deseja descobrir a lista, com **wsman /** como um prefixo.

## <a name="start-continuous-discovery"></a>Iniciar descoberta contínua

Conectar-se do dispositivo para hosts Hyper-V ou clusters e iniciar a descoberta VM.

1. Na **nome de usuário** e **senha**, especifique as credenciais da conta que o dispositivo usará para descobrir VMs. Especifique um nome amigável para as credenciais e, em seguida, clique em **salvar detalhes**.
2. Clique em **Adicionar host**e especifique os detalhes do host/cluster Hyper-V.
3. Clique em **Validar**. Após a validação, o número de VMs que podem ser descobertos em cada host/cluster é mostrado.
    - Se a validação falhar para um host, examine o erro passando o mouse sobre o ícone na **Status** coluna. Corrija os problemas e validar novamente.
    - Para remover hosts ou clusters, selecione > **excluir**.
    - Você não pode remover um host específico de um cluster. Você só pode remover todo o cluster.
    - Você pode adicionar um cluster, mesmo se houver problemas com hosts específicos no cluster.
4. Após a validação, clique em **salvar e iniciar a descoberta** para iniciar o processo de descoberta.

Isso inicia a descoberta. Demora cerca de 15 minutos para os metadados de VMs descobertas apareçam no portal do Azure. 

## <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a conclusão da descoberta, você pode verificar se as VMs aparecem no portal.

1. Abra o painel de migrações para Azure.
2. Na **migrações para Azure - servidores** > **migrações para Azure: Avaliação de servidor** página, clique no ícone que exibe a contagem para **detectados servidores**. 


## <a name="next-steps"></a>Próximas etapas

Experimente [avaliação do Hyper-V](tutorial-assess-hyper-v.md) com avaliação de servidor de migrar do Azure.
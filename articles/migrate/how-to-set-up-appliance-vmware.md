---
title: Configurar um dispositivo para Azure migrar avaliação/migração do servidor para VMs do VMware | Microsoft Docs
description: Descreve como configurar um dispositivo para a descoberta, avaliação e migração sem agente de VMs VMware usando o Azure migrar avaliação/migração do servidor.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811720"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurar um dispositivo para VMs do VMware

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando máquinas virtuais VMware com a ferramenta de avaliação de servidor de migrar do Azure ou migrar VMs VMware para o Azure com a migração sem agente usando a ferramenta de migração do servidor de migrar do Azure.

O dispositivo de VM do VMware é um dispositivo leve usado pelo Azure migrar avaliação/migração do servidor para fazer o seguinte:

- Descubra VMs locais do VMware.
- Envie dados de desempenho e de metadados para as VMs descobertas para Azure migrar avaliação/migração do servidor.

[Saiba mais](migrate-appliance.md) sobre o dispositivo de migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo de você:
- Baixar um arquivo de modelo OVA e importá-lo ao servidor do vCenter.
- Criar o dispositivo e verifique se ele pode se conectar à avaliação de servidor de migrar do Azure. 
- Configurar o dispositivo pela primeira vez e registrá-lo com o projeto de migrações para Azure.

## <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. Na **metas de migração** > **servidores** > **migrações para Azure: Avaliação de servidor**, clique em **Discover**.
2. Na **descobrir máquinas** > **os computadores são virtualizados?** , clique em **Sim, com o VMWare vSphere hypervisor**.
3. Clique em **baixar** para baixar o. Arquivo de modelo OVA.



### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro, antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão do dispositivo 1.0.0.5, o hash gerado deve corresponder a essas configurações. 

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Criar o dispositivo de VM

Importe o arquivo baixado e criar uma VM.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.
2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Na **nome** e **local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
5. Na **Host/Cluster**, especifique o host ou cluster no qual a VM será executado.
6. Na **armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
8. Na **mapeamento de rede**, especifique a rede à qual se conectar a VM. A rede precisa de conectividade com a internet para enviar metadados para a avaliação de servidor de migrar do Azure.
9. Revise e confirme as configurações e clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso de dispositivo para o Azure

Certifique-se de que o dispositivo de VM pode se conectar ao [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, fuso horário e a senha para o dispositivo.
3. Abra um navegador em qualquer computador que pode se conectar à VM e abra a URL do aplicativo web de dispositivo: **https://*nome do dispositivo ou o endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo da área de trabalho do dispositivo clicando no atalho do aplicativo.
4. No aplicativo web > **configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: Aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: O aplicativo verifica que a VM tem acesso à internet. Se a VM usa um proxy:
        - Clique em **as configurações de Proxy**e especifique o endereço de proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais caso o proxy exija autenticação.
        - Há suporte apenas para o proxy HTTP.
    - **Sincronização de tempo**: Tempo é verificado. A hora em que o dispositivo deve ser sincronia com o horário da internet para a descoberta funcione corretamente.
    - **Instalar atualizações**: As migrações para Azure verifica se as atualizações mais recentes do dispositivo estão instaladas.
    - **Instalar VDDK**: As migrações para Azure verifica se o VMWare vSphere Kit de desenvolvimento de disco Virtual (VDDK) está instalado.
        - Migrates do Azure usa o VDDK para replicar máquinas durante a migração para o Azure.
        - Baixe VDDK 6.7 do VMware e extraia o conteúdo do zip baixado para o local especificado no dispositivo.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo com as migrações para Azure

1. Clique em **faça logon no**. Se não for exibido, verifique se que você desabilitou o Bloqueador de pop-up no navegador.
2. Na nova guia, entre usando suas credenciais do Azure. 
    - Entrar com seu nome de usuário e senha.
    - Não há suporte para entrar com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo web.
2. Selecione a assinatura na qual o projeto de migrações para Azure foi criado. Em seguida, selecione o projeto.
3. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
4. Clique em **Registrar**.


## <a name="start-continuous-discovery"></a>Iniciar descoberta contínua

Agora, conecte-se do dispositivo ao vCenter Server e iniciar a descoberta VM. 

1. Na **especificar detalhes do vCenter Server**, especifique o FQDN (nome) ou o endereço IP do vCenter Server. Você pode deixar a porta padrão ou especificar uma porta personalizada na qual seu vCenter Server escuta.
2. Na **nome de usuário** e **senha**, especifique as credenciais de conta de somente leitura que o dispositivo usará para descobrir VMs no vCenter server. Certifique-se de que a conta tem o [permissões necessárias para a descoberta de](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Clique em **validar conexão** para certificar-se de que o dispositivo pode se conectar ao servidor do vCenter.
4. Depois que a conexão é estabelecida, clique em **salvar e iniciar a descoberta**.


Isso inicia a descoberta. Demora cerca de 15 minutos para os metadados de VMs descobertas apareçam no portal. 


## <a name="next-steps"></a>Próximas etapas

Analisar os tutoriais do [VMware assessment](tutorial-assess-vmware.md) e [migração sem agente](tutorial-migrate-vmware.md).

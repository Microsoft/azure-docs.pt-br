---
title: " Gerenciar um servidor de processo de escalonamento horizontal no Azure Site Recovery | Microsoft Docs"
description: Este artigo descreve como configurar e gerenciar um servidor de processo de escalonamento horizontal no Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: e5c01de19917235c34c035415df86291b9152bf0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-scale-out-process-server"></a>Gerenciar um servidor de processo de escalonamento horizontal

Um servidor de processo de escalonamento horizontal atua como um coordenador para transferência de dados entre os serviços do Site Recovery e sua infraestrutura local. Este artigo descreve como você pode definir, configurar e gerenciar um servidor de processo de escalonamento horizontal.

## <a name="prerequisites"></a>Pré-requisitos
A seguir estão o hardware e software recomendados e a configuração de rede necessária para configurar um servidor de processo de escalonamento horizontal.

> [!NOTE]
> [Planejamento de capacidade](site-recovery-capacity-planner.md) é uma etapa importante para assegurar que você implante o servidor de processo de escalonamento horizontal com uma configuração que se adeque aos seus requisitos de carga. Leia mais sobre [Características de dimensionamento de um servidor de processo de escalonamento horizontal](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Baixar o software para servidores de processo de escalonamento horizontal
1. Faça logon no Portal do Azure e navegue até seu Cofre dos Serviços de Recuperação.
2. Navegue até **Infraestrutura do Site Recovery** > **Servidores de Configuração** (sob Para VMware e Computadores Físicos).
3. Selecione o servidor de configuração para fazer drill down na página de detalhes do servidor de configuração.
4. Clique no botão **+Servidor de Processo**.
5. Na página **Adicionar servidor de processo**, selecione a opção **Implantar um servidor de processo de escalonamento horizontal local** na lista suspensa **Escolher onde você deseja implantar o servidor de processo**.

  ![Página Adicionar Servidores](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Clique no link **Baixar a instalação unificada do Microsoft Azure Site Recovery** para baixar a versão mais recente da instalação do servidor de processo de escalonamento horizontal.

  > [!WARNING]
  A versão do seu servidor de processo de escalonamento horizontal deve ser igual ou menor que a versão do servidor de configuração em execução no seu ambiente. Uma maneira simples de garantir a compatibilidade de versão é usar os mesmos bits do instalador usados recentemente para instalar/atualizar o servidor de configuração.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Instalar e registrar um servidor de processo de escalonamento horizontal por meio da GUI
Se for necessário escalar horizontalmente sua implantação para além de 200 computadores de origem ou uma taxa diária de rotatividade total acima de 2 TB, você precisará de servidores em processo adicionais para manipular o volume de tráfego.

Verifique as [recomendações de tamanho para servidores em processo](#size-recommendations-for-the-process-server) e siga estas instruções para configurar o servidor em processo. Depois de configurar o servidor, você migrará os computadores de origem para usá-lo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Instalar e registrar um servidor de processo de escalonamento horizontal usando a linha de comando

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Amostra de uso
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Argumentos de linha de comando do instalador do servidor de processo de escalonamento horizontal.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Criar um arquivo de configuração de configurações de proxy
O parâmetro ProxySettingsFilePath utiliza um arquivo como entrada. Crie o arquivo usando o formato a seguir e passe-o como o parâmetro ProxySettingsFilePath de entrada.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Modificando configurações de proxy do servidor de processo de escalonamento horizontal
1. Faça logon no servidor de processo de escalonamento horizontal.
2. Abra uma janela de comando do PowerShell do Administrador.
3. Execute o comando a seguir
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Em seguida, navegue até o diretório **%PROGRAMDATA%\ASR\Agent** e execute o comando a seguir
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Registrando novamente um servidor de processo de escalonamento horizontal
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Em seguida, abra um prompt de comando de Administrador.
* Navegue até o diretório **%PROGRAMDATA%\ASR\Agent** e execute o comando

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Atualizando um servidor de processo de escalonamento horizontal
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Desativando um servidor de processo de escalonamento horizontal
1. Verifique se:
  - O estado de conexão do servidor de configuração é mostrado como **Conectado** no Portal do Azure
  - O servidor de processo ainda pode se comunicar com o servidor de configuração.
2. Fazer logon no servidor de processo como administrador
3. Abrir o Painel de Controle > Programa > Desinstalar Programas
4. Desinstale os programas na sequência fornecida a seguir:
  * Servidor de processo/servidor de configuração do Microsoft Azure Site Recovery
  * Dependências de servidor de configuração do Microsoft Azure Site Recovery
  * Agente de Serviços dos Serviços de Recuperação do Microsoft Azure

Pode levar até 15 minutos para que a exclusão do servidor de processo reflita no Portal do Azure.

  > [!NOTE]
  Se o servidor de processo não for capaz de se comunicar com o servidor de configuração (o estado de conexão no portal será Desconectado), você precisará seguir as etapas a seguir para limpá-lo do servidor de configuração.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Cancelando o registro de um servidor de processo de escalonamento horizontal desconectado de um servidor de configuração

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Requisitos de dimensionamento para um servidor de processo de escalonamento horizontal

| **Servidor de processo adicional** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- |
|4 vCPUs (2 soquetes * 2 núcleos @ 2,5 GHz), 8 GB de memória |300 GB |250 GB ou menos |Replique 85 computadores ou menos. |
|8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz), 12 GB de memória |600 GB |250 GB a 1 TB |Replique entre 85 e 150 computadores. |
|12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz), 24 GB de memória |1 TB |1 TB a 2 TB |Replique entre 150 e 225 computadores. |

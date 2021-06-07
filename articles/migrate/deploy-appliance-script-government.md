---
title: Configurar um dispositivo de migrações para Azure no Azure governamental
description: Saiba como configurar um dispositivo de migrações para Azure no Azure governamental
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: c4ca8d8ac24ac174158957e44b5eabe4a89a5340
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775197"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Configurar um dispositivo no Azure governamental 

Siga este artigo para implantar um [dispositivo de migrações para Azure](./migrate-appliance-architecture.md) para servidores no ambiente VMware, servidores no Hyper-V e servidores físicos, em uma nuvem do Azure governamental. Você executa um script para criar o dispositivo e verifica se ele pode se conectar ao Azure. Se você quiser configurar um dispositivo na nuvem pública, siga [Este artigo](deploy-appliance-script.md).


> [!NOTE]
> A opção de implantar um dispositivo usando um modelo (para servidores no ambiente VMware e ambiente Hyper-V) não tem suporte no Azure governamental.


## <a name="prerequisites"></a>Pré-requisitos

O script configura o dispositivo migrações para Azure em um servidor físico existente ou em um servidor virtualizado.

- O servidor que atuará como o dispositivo deve estar executando o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo. Ele requer um endereço IP estático ou dinâmico e o acesso à Internet.
- Antes de implantar o dispositivo, examine os requisitos de dispositivo detalhados para [servidores no VMware](migrate-appliance.md#appliance---vmware), [no Hyper-V e em](migrate-appliance.md#appliance---hyper-v) [servidores físicos](migrate-appliance.md#appliance---physical).
- Não execute o script em um dispositivo de migrações do Azure existente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurar o dispositivo para VMware

Para configurar o dispositivo para VMware, você baixa um arquivo compactado do portal do Azure e extrai o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto.

### <a name="download-the-script"></a>Baixar o script

1. Em **metas de migração**  >  , migrações para **Windows, Linux e SQL Servers**  >  **do Azure: descoberta e avaliação**, clique em **descobrir**.
2. Em **descobrir servidor**  >  **os servidores são virtualizados?**, selecione **Sim, com VMware vSphere hipervisor**.
3. Clique em **baixar** para baixar o arquivo compactado.

### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No servidor no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Verifique a versão mais recente do dispositivo e o valor de hash:

    **Algoritmo** | **Download** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Executar o script

Veja o que o script faz:

- Instala agentes e um aplicativo Web.
- Instala funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o PowerShell ISE.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM), com configurações persistentes para migrações para Azure.
- Cria arquivos de log e de configuração da seguinte maneira:
    - **Arquivos de configuração**:%ProgramData%\Microsoft Azure\Config
    - **Arquivos de log**:%ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia o arquivo compactado para uma pasta no servidor que hospedará o dispositivo. Certifique-se de não executar o script em um servidor com um dispositivo de migrações para Azure existente.
2. Inicie o PowerShell no servidor, com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte maneira:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar o acesso

Verifique se o dispositivo pode se conectar a URLs do Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Configurar o dispositivo para Hyper-V

Para configurar o dispositivo para Hyper-V, você baixa um arquivo compactado do portal do Azure e extrai o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto.

### <a name="download-the-script"></a>Baixar o script

1.  Em **metas de migração**  >  , migrações para **Windows, Linux e SQL Servers**  >  **do Azure: descoberta e avaliação**, clique em **descobrir**.
2.  Em **descobrir servidores**  >  **os servidores são virtualizados?**, selecione **Sim, com o Hyper-V**.
3.  Clique em **baixar** para baixar o arquivo compactado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No servidor no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Verifique a versão mais recente do dispositivo e o valor de hash:

    **Cenário** | **Download** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Executar o script

Veja o que o script faz:

- Instala agentes e um aplicativo Web.
- Instala funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o PowerShell ISE.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM), com configurações persistentes para migrações para Azure.
- Cria arquivos de log e de configuração da seguinte maneira:
    - **Arquivos de configuração**:%ProgramData%\Microsoft Azure\Config
    - **Arquivos de log**:%ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia o arquivo compactado para uma pasta no servidor que hospedará o dispositivo. Certifique-se de não executar o script em um servidor que executa um dispositivo de migrações para Azure existente.
2. Inicie o PowerShell no servidor, com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte maneira: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar o acesso

Verifique se o dispositivo pode se conectar a URLs do Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurar o dispositivo para servidores físicos

Para configurar o dispositivo para VMware, você baixa um arquivo compactado do portal do Azure e extrai o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto.

### <a name="download-the-script"></a>Baixar o script

1. Em **metas de migração**  >  , migrações para **Windows, Linux e SQL Servers**  >  **do Azure: descoberta e avaliação**, clique em **descobrir**.
2. Em **descobrir servidores**  >  **os servidores são virtualizados?**, selecione **não virtualizados/outros**.
3. Clique em **baixar** para baixar o arquivo compactado.

### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. Nos servidores nos quais você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Verifique a versão mais recente do dispositivo e o valor de hash:

    **Cenário** | **Baixar** _ | _ *Valor de hash**
    --- | --- | ---
    Físico (85 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Executar o script

Veja o que o script faz:

- Instala agentes e um aplicativo Web.
- Instala funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o PowerShell ISE.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM), com configurações persistentes para migrações para Azure.
- Cria arquivos de log e de configuração da seguinte maneira:
    - **Arquivos de configuração**:%ProgramData%\Microsoft Azure\Config
    - **Arquivos de log**:%ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia o arquivo compactado para uma pasta no servidor que hospedará o dispositivo. Certifique-se de não executar o script em um servidor que executa um dispositivo de migrações para Azure existente.
2. Inicie o PowerShell no servidor, com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte maneira:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar o acesso

Verifique se o dispositivo pode se conectar a URLs do Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Próximas etapas

Depois de implantar o dispositivo, você precisa configurá-lo pela primeira vez e registrá-lo com o projeto.

- Configure o dispositivo para [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configure o dispositivo para o [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configure o dispositivo para [servidores físicos](how-to-set-up-appliance-physical.md).

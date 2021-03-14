---
title: Configurar um dispositivo de migrações para Azure com um script
description: Saiba como configurar um dispositivo de migrações para Azure com um script
ms.topic: how-to
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.date: 04/16/2020
ms.openlocfilehash: abf34a5ea83e74b0bd2a074108c5ef1014941093
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041324"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurar um dispositivo com um script

Siga este artigo para criar um [dispositivo de migrações para Azure](./migrate-appliance-architecture.md) para a avaliação/migração de VMs VMware e VMs do Hyper-V. Você executa um script para criar um dispositivo e verifica se ele pode se conectar ao Azure. 

Você pode implantar o dispositivo para VMs do VMware e do Hyper-V usando um script ou usando um modelo que você baixa do portal do Azure. Usar um script será útil se você não conseguir criar uma VM usando o modelo baixado.

- Para usar um modelo, siga os tutoriais para [VMware](./tutorial-discover-vmware.md) ou [Hyper-V](./tutorial-discover-hyper-v.md).
- Para configurar um dispositivo para servidores físicos, você só pode usar um script. Siga [Este artigo](how-to-set-up-appliance-physical.md).
- Para configurar um dispositivo em uma nuvem do Azure governamental, siga [Este artigo](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Pré-requisitos

O script configura o dispositivo migrações para Azure em uma VM ou máquina física existente.

- O computador que atuará como o dispositivo deve atender aos seguintes requisitos de hardware e de sistema operacional:

Cenário | Requisitos
--- | ---
VMware | Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco
Hyper-V | Windows Server 2016, com 16 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco
- O computador também precisa de um comutador virtual externo. Ele requer um endereço IP estático ou dinâmico e o acesso à Internet.
- Antes de implantar o dispositivo, examine os requisitos de dispositivo detalhados para [VMs do VMware](migrate-appliance.md#appliance---vmware), [VMs do Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Não execute o script em um dispositivo de migrações do Azure existente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurar o dispositivo para VMware

Para configurar o dispositivo para VMware, baixe o arquivo compactado chamado AzureMigrateInstaller-Server-Public.zip no portal ou [aqui](https://go.microsoft.com/fwlink/?linkid=2140334)e extraia o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto de migrações para Azure.


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Verifique a versão mais recente do dispositivo e o script para a nuvem pública do Azure:

    **Algoritmo** | **Download** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72



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

1. Extraia o arquivo compactado em uma pasta no computador que hospedará o dispositivo. Você não deve executar o script em um computador em um dispositivo de Migrações para Azure existente.
2. Inicie o PowerShell no computador com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte maneira:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar o acesso

Verifique se o dispositivo pode se conectar às URLs do Azure para a nuvem [pública](migrate-appliance.md#public-cloud-urls) .

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurar o dispositivo para Hyper-V

Para configurar o dispositivo para Hyper-V, baixe o arquivo compactado chamado AzureMigrateInstaller-Server-Public.zip no portal ou [aqui](https://go.microsoft.com/fwlink/?linkid=2105112)e extraia o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto de migrações para Azure.


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Verifique a versão mais recente do dispositivo e o script para a nuvem pública do Azure:

    **Cenário** | **Download** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

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

1. Extraia o arquivo compactado em uma pasta no computador que hospedará o dispositivo. Você não deve executar o script em um computador em um dispositivo de Migrações para Azure existente.
2. Inicie o PowerShell no computador com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte maneira: 

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar o acesso

Verifique se o dispositivo pode se conectar às URLs do Azure para a nuvem [pública](migrate-appliance.md#public-cloud-urls) .

## <a name="next-steps"></a>Próximas etapas

Depois de implantar o dispositivo, você precisa configurá-lo pela primeira vez e registrá-lo com o projeto de migrações para Azure.

- Configure o dispositivo para [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configure o dispositivo para o [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
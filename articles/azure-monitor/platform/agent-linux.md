---
title: Instalar o Agente do Log Analytics em computadores Linux
description: Este artigo descreve como conectar computadores Linux hospedados em outras nuvens ou locais para Azure Monitor com o agente de Log Analytics para Linux.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: e1dbf5e20aa206189397cab26e9b867f4942e1d5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886831"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Instalar o Agente do Log Analytics em computadores Linux
Este artigo fornece detalhes sobre como instalar o agente de Log Analytics em computadores Linux usando os seguintes métodos:

* [Instale o agente para Linux usando um script wrapper](#install-the-agent-using-wrapper-script) hospedado no github. Esse é o método recomendado para instalar e atualizar o agente quando o computador tem conectividade com a Internet, diretamente ou por meio de um servidor proxy.
* [Baixe e instale manualmente](#install-the-agent-manually) o agente. Isso é necessário quando o computador Linux não tem acesso à Internet e se comunicará com o Azure Monitor ou a automação do Azure por meio do [Gateway de log Analytics](gateway.md). 

>[!IMPORTANT]
> Os métodos de instalação descritos neste artigo normalmente são usados para máquinas virtuais locais ou em outras nuvens. Consulte [Opções de instalação](log-analytics-agent.md#installation-options) para obter opções mais eficientes que você pode usar para máquinas virtuais do Azure.



## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

Consulte [visão geral dos agentes de Azure monitor](agents-overview.md#supported-operating-systems) para obter uma lista de distribuições do Linux com suporte pelo agente de log Analytics.

>[!NOTE]
>OpenSSL 1.1.0 só tem suporte em plataformas de x86_x64 (64 bits) e OpenSSL mais cedo do que 1. x não tem suporte em qualquer plataforma.

>[!NOTE]
>Não há suporte para a execução do agente do Log Analytics Linux em contêineres. Se você precisar monitorar contêineres, aproveite a [solução de monitoramento de contêiner](../insights/containers.md) para hosts do Docker ou [Azure monitor para contêineres](../insights/container-insights-overview.md) para kubernetes.

Começando com versões lançadas depois de agosto de 2018, estamos fazendo as seguintes alterações ao nosso modelo de suporte:  

* Somente o servidor versões têm suporte, um não cliente.  
* Concentre o suporte em uma das [Distribuições endossadas do Linux do Azure ](../../virtual-machines/linux/endorsed-distros.md). Observe que pode haver algum atraso entre uma nova distribuição/versão sendo endossada pelo Linux do Azure e seu suporte ao Agente do Log Analytics Linux.
* Todas as versões secundárias têm suporte para cada versão principal listada.
* As versões que passaram a data de fim de suporte do fabricante não são suportadas.
* Suporte apenas a imagens de VM; os contêineres, mesmo aqueles derivados de imagens oficiais do distribuição Publishers, não têm suporte.
* Não há suporte para novas versões do AMI.  
* Apenas versões que executam o SSL 1.x por padrão são suportadas.

>[!NOTE]
>Se você estiver usando uma distribuição ou versão que não é suportada no momento e não se alinha ao nosso modelo de suporte, recomendamos que você distribua esse repositório, reconhecendo que o suporte da Microsoft não fornecerá assistência com as versões do agente bifurcado.

### <a name="python-requirement"></a>Requisito do Python

A partir do Agent versão 1.13.27, o agente do Linux dará suporte a Python 2 e 3. É sempre recomendável usar o agente mais recente. 

Se você estiver usando uma versão mais antiga do agente, deverá ter a máquina virtual usar o Python 2 por padrão. Se sua máquina virtual estiver usando um distribuição que não inclua o Python 2 por padrão, você deverá instalá-lo. Os comandos de exemplo a seguir instalarão o Python 2 em distribuições diferentes.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

O executável python2 deve ter um alias para *Python*. A seguir, um método que você pode usar para definir este alias:

1. Execute o comando a seguir para remover todos os aliases existentes.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Execute o comando a seguir para criar o alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Proteção do Linux com suporte
O agente do OMS tem suporte limitado para personalização para Linux. 

No momento, há suporte para os seguintes: 
- FIPs

A seguir está em consideração, mas ainda não há suporte para isso:
- ICS
- SELINUX

Outros métodos de proteção e personalização não são suportados nem planejados para o agente do OMS.  

## <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela a seguir realça os pacotes necessários para [distribuições do Linux com suporte](#supported-operating-systems) em que o agente será instalado.

|Pacote necessário |Descrição |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas OpenSSL | 1.0.x ou 1.1.x |
|Curl | cliente Web cURL | 7.15.5 |
|Python | | 2.6 + ou 3.3 +
|Python-ctypes | | 
|PAM | Módulos de autenticação conectáveis | | 

>[!NOTE]
>Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.

## <a name="network-requirements"></a>Requisitos de rede
Consulte [visão geral do agente de log Analytics](log-analytics-agent.md#network-requirements) para os requisitos de rede para o agente do Linux.

## <a name="agent-install-package"></a>Pacote de instalação do agente

O agente do Log Analytics para Linux é composto por vários pacotes. O arquivo de versão contém os seguintes pacotes, que estão disponíveis ao executar o pacote de shell com o `--extract` parâmetro:

**Pacote** | **Versão** | **Descrição**
----------- | ----------- | --------------
omsagent | 1.13.9 | O agente de Log Analytics para Linux
omsconfig | 1.1.1 | Agente de configuração para o agente de Log Analytics
omi | 1.6.4 | OMI (infraestrutura de gerenciamento aberta) – um servidor CIM leve. *Observe que o OMI requer acesso de raiz para executar um trabalho cron necessário para o funcionamento do serviço*
scx | 1.6.4 | Provedores de CIM OMI para métricas de desempenho do sistema operacional
apache-cimprov | 1.0.1 | Provedor de monitoramento de desempenho do Servidor HTTP Apache para OMI. Instalado somente se o Servidor HTTP Apache for detectado.
mysql-cimprov | 1.0.1 | Provedor de monitoramento de desempenho do Servidor MySQL para OMI. Instalado somente se o servidor MySQL/MariaDB for detectado.
docker-cimprov | 1.0.0 | Provedor do Docker para OMI. Instalado somente se o Docker for detectado.

### <a name="agent-installation-details"></a>Detalhes de instalação do agente

Depois de instalar o agente de Log Analytics para pacotes do Linux, as seguintes alterações de configuração adicionais em todo o sistema são aplicadas. Esses artefatos serão removidos quando o pacote omsagent for desinstalado.

* Um usuário sem privilégios chamado: `omsagent` é criado. O daemon é executado nessa credencial. 
* Um arquivo de *inclusão* do sudors é criado no `/etc/sudoers.d/omsagent` . Isso autoriza `omsagent` a reiniciar os daemons syslog e omsagent. Se o sudo *incluir* diretivas não tiver suporte na versão instalada do sudo, essas entradas serão gravadas no `/etc/sudoers` .
* A configuração de syslog é modificada para encaminhar um subconjunto de eventos para o agente. Para obter mais informações, consulte [Configure syslog Data Collection](data-sources-syslog.md).

Em um computador Linux monitorado, o agente é listado como `omsagent` . `omsconfig` é o agente de configuração do Log Analytics Agent para Linux que procura a nova configuração do lado do portal a cada 5 minutos. A configuração nova e atualizada é aplicada aos arquivos de configuração do agente localizados em `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Instalar o agente usando o script de wrapper

As etapas a seguir configuram a instalação do agente para Log Analytics no Azure e na nuvem do Azure governamental usando o script de wrapper para computadores Linux que podem se comunicar diretamente ou por meio de um servidor proxy para baixar o agente hospedado no GitHub e instalar o agente.  

Se o seu computador Linux precisar se comunicar por meio de um servidor proxy para Log Analytics, essa configuração poderá ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]` . A propriedade *Protocol* aceita `http` ou `https` , e a propriedade *proxyHost* aceita um nome de domínio totalmente qualificado ou um endereço IP do servidor proxy. 

Por exemplo: `https://proxy01.contoso.com:30443`

Se a autenticação for necessária em ambos os casos, você precisará especificar o nome de usuário e a senha. Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para se conectar a um Log Analytics espaço de trabalho, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária. O comando a seguir baixa o agente, valida sua soma de verificação e o instala.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O seguinte comando inclui o parâmetro de proxy `-p` e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para se conectar ao workspace do Log Analytics na nuvem do Azure Governamental, execute o seguinte comando fornecendo a ID do workspace e a chave primária copiadas anteriormente. O comando a seguir baixa o agente, valida sua soma de verificação e o instala. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O seguinte comando inclui o parâmetro de proxy `-p` e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Reinicie o agente executando o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Instalar o agente manualmente

O agente de Log Analytics para Linux é fornecido em um pacote de script de Shell de extração automática e instalável. Este pacote contém pacotes Debian e RPM para cada um dos componentes do agente e pode ser instalado diretamente ou extraído para recuperar os pacotes individuais. Um pacote é fornecido para x64 e outro para arquiteturas x86. 

> [!NOTE]
> Para VMs do Azure, recomendamos que você instale o agente neles usando a [extensão de VM log Analytics do Azure](../../virtual-machines/extensions/oms-linux.md) para Linux. 

1. [Baixe](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) e transfira o pacote apropriado (x64 ou x86) para sua VM do Linux ou computador físico, usando o SCP/SFTP.

2. Instale o pacote usando o `--install` argumento. Para carregar um espaço de trabalho do Log Analytics durante a instalação, forneça os `-w <WorkspaceID>` `-s <workspaceKey>` parâmetros e copiados anteriormente.

    >[!NOTE]
    >Você precisará usar o `--upgrade` argumento se algum pacote dependente, como OMI, SCX, programa omsconfig ou suas versões mais antigas forem instalados, como seria o caso se o agente do System Center Operations Manager para Linux já estiver instalado. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Para configurar o agente do Linux para instalar e conectar-se a um Log Analytics espaço de trabalho por meio de um gateway de Log Analytics, execute o comando a seguir fornecendo os parâmetros proxy, ID do espaço de trabalho e chave do espaço de trabalho. Essa configuração pode ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]` . A propriedade *proxyHost* aceita um nome de domínio totalmente qualificado ou endereço IP do servidor de gateway de log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Se a autenticação for necessária, você precisará especificar o nome de usuário e a senha. Por exemplo: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Para configurar o computador Linux para se conectar a um Log Analytics espaço de trabalho na nuvem do Azure governamental, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária copiadas anteriormente.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Se você quiser instalar os pacotes de agente e configurá-lo para relatar para um espaço de trabalho específico do Log Analytics em um momento posterior, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Se você quiser extrair os pacotes de agente do pacote sem instalar o agente, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Atualizar de uma versão anterior

A atualização de uma versão anterior, começando com a versão 1.0.0-47, tem suporte em cada versão. Execute a instalação com o `--upgrade` parâmetro para atualizar todos os componentes do agente para a versão mais recente.

## <a name="cache-information"></a>Informações de cache
Os dados do agente de Log Analytics para Linux são armazenados em cache no computador local em *% STATE_DIR_WS%/out_oms_common*. buffer * antes de ser enviado para Azure monitor. Os dados de log personalizados são armazenados em buffer em *% STATE_DIR_WS%/out_oms_blob*. buffer *. O caminho pode ser diferente para algumas [soluções e tipos de dados](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=).

O agente tenta carregar a cada 20 segundos. Se falhar, ele aguardará um período de tempo cada vez maior até que tenha êxito: 30 segundos antes da segunda tentativa, 60 segundos antes da terceira, 120 segundos... e até um máximo de 16 minutos entre as repetições até que ele se conecte com êxito novamente. O agente tentará novamente até seis vezes para um determinado bloco de dados antes de descartar e passar para o próximo. Isso continuará até que o agente possa ser carregado com êxito novamente. Isso significa que os dados podem ser armazenados em buffer em até aproximadamente 30 minutos antes de serem descartados.

O tamanho de cache padrão é 10 MB, mas pode ser modificado no [arquivo omsagent. conf](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf).


## <a name="next-steps"></a>Próximas etapas

- Examinar [Gerenciar e manter o agente de Log Analytics para o Windows e Linux](agent-manage.md) para aprender sobre como reconfigurar, atualizar ou remover o agente da máquina virtual.

- Consulte [Solução de problemas do agente do Linux](agent-linux-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente.

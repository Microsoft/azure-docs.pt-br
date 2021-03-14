---
title: Solucionar problemas do agente do Linux para o Azure Log Analytics | Microsoft Docs
description: Descreva os sintomas, as causas e a resolução dos problemas mais comuns com o agente de Log Analytics para Linux no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: f8558644fc24f3190247e57b4de7228af161a2a8
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631159"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Como solucionar problemas com o agente do Log Analytics para Linux 

Este artigo fornece ajuda para solucionar erros que podem ocorrer com o Log Analytics Agent para Linux no Azure Monitor e sugere possíveis soluções para resolvê-los.

Se nenhuma dessas etapas funcionar para você, os seguintes canais de suporte também estarão disponíveis:

* Os clientes com benefícios de suporte Premier podem abrir uma solicitação de suporte com [Premier](https://premier.microsoft.com/).
* Os clientes com contratos de suporte do Azure podem abrir uma solicitação de suporte [no portal do Azure](https://manage.windowsazure.com/?getsupport=true).
* Diagnostique os problemas do OMI com o [Guia para a solução de problemas da OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Arquivo de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Visite a página de comentários Log Analytics para revisar ideias e bugs enviados [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) ou um novo arquivo. 

## <a name="log-analytics-troubleshooting-tool"></a>Ferramenta de solução de problemas Log Analytics

A ferramenta de solução de problemas do Log Analytics Agent para Linux é um script criado para ajudar a localizar e diagnosticar problemas com o agente de Log Analytics. Ele é incluído automaticamente com o agente na instalação. A execução da ferramenta deve ser a primeira etapa no diagnóstico de um problema.

### <a name="how-to-use"></a>Como usar
A ferramenta de solução de problemas pode ser executada colando o seguinte comando em uma janela de terminal em um computador com o agente de Log Analytics: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Instalação manual
A ferramenta de solução de problemas é incluída automaticamente na instalação do agente de Log Analytics. No entanto, se a instalação falhar de alguma forma, ela também poderá ser instalada manualmente seguindo as etapas abaixo.

1. Copie o pacote de solução de problemas em seu computador: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. Descompacte o pacote: `tar -xzvf omsagent_tst.tar.gz`
3. Execute a instalação manual: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Cenários abordados
Abaixo está uma lista de cenários verificados pela ferramenta de solução de problemas:

1. O agente não está íntegro, a pulsação não funciona corretamente
2. O agente não é iniciado, não é possível conectar-se aos serviços analíticos de log
3. O syslog do agente não está funcionando
4. O agente tem alto uso de CPU/memória
5. Agente com problemas de instalação
6. Os logs personalizados do agente não estão funcionando
7. Coletar logs do agente

Para obter mais detalhes, consulte nossa [documentação do GitHub](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md).

 >[!NOTE]
 >Execute a ferramenta coletor de logs quando ocorrer um problema. Inicialmente, ter os logs ajudará muito a nossa equipe de suporte a solucionar o problema mais rapidamente.

## <a name="purge-and-re-install-the-linux-agent"></a>Limpar e Re-Install o agente do Linux

Vimos que uma reinstalação limpa do agente corrigirá a maioria dos problemas. Na verdade, essa pode ser a primeira sugestão do suporte para colocar o agente em um estado uncurropted de nossa equipe de suporte. Executar a solução de problemas, coletar logs e tentar uma reinstalação limpa ajudará a resolver problemas mais rapidamente.

1. Baixe o script de limpeza:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Execute o script de limpeza (com permissões sudo):
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Ferramenta do coletor de Log e locais de logs importantes

 Arquivo | Caminho
 ---- | -----
 Arquivo de log do agente do Log Analytics para Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Arquivo de Log de Configuração do Agente Log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Recomendamos que você use a nossa ferramenta coletor de logs para recuperar registros importantes para a solução de problemas ou antes de enviar um problema do GitHub. Você pode ler mais sobre a ferramenta e como executá-la [aqui](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Arquivos de configuração importantes

 Category | Local do arquivo
 ----- | -----
 syslog | `/etc/syslog-ng/syslog-ng.conf` ou `/etc/rsyslog.conf` ou `/etc/rsyslog.d/95-omsagent.conf`
 Desempenho, Nagios, Zabbix, saída do Log Analytics e agente geral | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Configurações adicionais | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >A edição de arquivos de configuração para contadores de desempenho e Syslog será sobrescrita se a coleta for configurada no [menu de dados Configurações Avançadas do Log Analytics](../agents/agent-data-sources.md#configuring-data-sources) no portal do Azure para seu workspace. Para desativar a configuração para todos os agentes, desative a coleta em **Configurações Avançadas** do Log Analytics ou, para um único agente, execute o seguinte:  
> `sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable && sudo rm /etc/opt/omi/conf/omsconfig/configuration/Current.mof* /etc/opt/omi/conf/omsconfig/configuration/Pending.mof*`

## <a name="installation-error-codes"></a>Códigos de erro de instalação

| Código do Erro | Significado |
| --- | --- |
| NOT_DEFINED | Como as dependências necessárias não estão instaladas, o plug-in auditd do auoms não será instalado | A instalação do auoms falhou, instale o pacote auditd. |
| 2 | Opção inválida fornecida para o pacote de shell. Executar `sudo sh ./omsagent-*.universal*.sh --help` para uso |
| 3 | Nenhuma opção fornecida para o pacote de shell. Executar `sudo sh ./omsagent-*.universal*.sh --help` para uso. |
| 4 | Tipo de pacote inválido OU configurações de proxy inválidas; Pacotes omsagent- *rpm* .sh só podem ser instalados em sistemas baseados em RPM, e pacotes omsagent- *deb* .sh só podem ser instalados em sistemas baseados em Debian. É recomendado que você use o instalador universal da [última versão](../vm/quick-collect-linux-computer.md#install-the-agent-for-linux). Também examine para verificar as configurações de proxy. |
| 5 | O pacote do shell deve ser executado como raiz OU houve erro 403 retornado durante a integração. Execute o comando usando `sudo`. |
| 6 | Arquitetura de pacotes inválida OU ocorreu um erro. Erro 200 retornado durante a integração; os pacotes x64.sh do omsagent- *só podem ser instalados em sistemas de 64 bits, e os pacotes x64.sh do omsagent-* só podem ser instalados em sistemas de 32 bits. Faça o download do pacote correto para sua arquitetura da [última versão](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | A instalação do pacote do OMS falhou. Examine a saída do comando para a falha raiz. |
| 18 | Falha na instalação do pacote programa omsconfig. Examine a saída do comando para a falha raiz. |
| 19 | A instalação do pacote OMI falhou. Examine a saída do comando para a falha raiz. |
| 20 | A instalação do pacote SCX falhou. Examine a saída do comando para a falha raiz. |
| 21 | A instalação de kits do provedor falhou. Examine a saída do comando para a falha raiz. |
| 22 | Falha na instalação do pacote único. Examinar a saída do comando para a falha de raiz |
| 23 | Pacote SCX ou OMI já instalado. Use `--upgrade` em vez de `--install` para instalar o pacote do shell. |
| 30 | Erro interno do pacote. Relate um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com os detalhes do resultado. |
| 55 | Versão sem suporte do OpenSSL ou não é possível conectar-se a Azure Monitor ou dpkg está bloqueado ou faltando programa de rotação. |
| 61 | Biblioteca de ctypes do Python ausente. Instale a biblioteca ou pacote ctypes do Python (python-ctypes). |
| 62 | O programa tar está ausente, instale-o. |
| 63 | O programa sed está ausente, instale-o. |
| 64 | Programa de curl ausente, instale curl. |
| 65 | Programa gpg ausente, gpg de instalação. |

## <a name="onboarding-error-codes"></a>Códigos de erro de integração

| Código do Erro | Significado |
| --- | --- |
| 2 | Opção inválida fornecida ao script omsadmin. Executar `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para uso. |
| 3 | Configuração inválida fornecida ao script omsadmin. Executar `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para uso. |
| 4 | Proxy inválido fornecido ao script omsadmin. Verifique o proxy e consulte nossa [documentação para usar um proxy HTTP ](./log-analytics-agent.md#firewall-requirements). |
| 5 | 403 erro HTTP recebido de Azure Monitor. Ver a saída completa do script omsadmin para obter detalhes. |
| 6 | Erro de HTTP não 200 recebido de Azure Monitor. Ver a saída completa do script omsadmin para obter detalhes. |
| 7 | Não é possível conectar-se ao Azure Monitor. Ver a saída completa do script omsadmin para obter detalhes. |
| 8 | Erro ao integrar o espaço de trabalho do Log Analytics. Ver a saída completa do script omsadmin para obter detalhes. |
| 30 | Erro interno do script. Relate um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com os detalhes do resultado. |
| 31 | Erro ao gerar o ID do agente. Relate um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com os detalhes do resultado. |
| 32 | Erro ao gerar certificados. Ver a saída completa do script omsadmin para obter detalhes. |
| 33 | Erro ao gerar meta-configuração para o omsconfig. Relate um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com os detalhes do resultado. |
| 34 | Script de geração de meta-configuração não presente. Tente novamente o onboarding com `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Habilitar o log de depuração
### <a name="oms-output-plugin-debug"></a>Depuração do plugin de saída do OMS
 O FluentD permite níveis de registro específicos do plugin, permitindo que você especifique diferentes níveis de registro para entradas e saídas. Para especificar um nível de log diferente para a saída do OMS, edite a configuração geral do agente em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 No plug-in de saída do OMS, antes do final do arquivo de configuração, altere a propriedade `log_level` de `info` para `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

O log de depuração permite que você veja os carregamentos em lote para Azure Monitor separados por tipo, número de itens de dados e tempo gasto para enviar:

*Log habilitado para depuração de exemplo:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Saída detalhada
Em vez de usar o plug-in de saída do OMS, também é possível exportar itens de dados diretamente para `stdout`, o que é visível no arquivo de log do agente Log Analytics para Linux.

No arquivo de configuração do agente geral do Log Analytics em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, comente o plug-in de saída do OMS adicionando um `#` na frente de cada linha:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Abaixo do plug-in de saída, remova o comentário da seção a seguir removendo o `#` na frente de cada linha:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problema: não é possível se conectar por meio de proxy para Azure Monitor

### <a name="probable-causes"></a>Causas prováveis
* O proxy especificado durante a integração estava incorreto
* Os pontos de extremidade do Azure Monitor e do serviço de automação do Azure não estão incluídos na lista aprovada em seu datacenter 

### <a name="resolution"></a>Resolução
1. Reintegre para Azure Monitor com o agente de Log Analytics para Linux usando o comando a seguir com a opção `-v` habilitada. Ele permite a saída detalhada do agente que se conecta por meio do proxy para Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Examine a seção [Atualizar as configurações de proxy](agent-manage.md#update-proxy-settings) para verificar se você configurou corretamente o agente para se comunicar por meio de um servidor proxy.    

3. Verifique se os pontos de extremidade descritos na lista Azure Monitor requisitos de firewall de [rede](./log-analytics-agent.md#firewall-requirements) foram adicionados corretamente a uma lista de permissões. Se você usar a automação do Azure, as etapas de configuração de rede necessárias também serão vinculadas acima.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: você recebe um erro 403 quando tentar integrar

### <a name="probable-causes"></a>Causas prováveis
* A data e a hora estão incorretas no servidor Linux 
* A ID e a Chave do Workspace usadas estão incorretas

### <a name="resolution"></a>Resolução

1. Verifique a hora no servidor Linux com o comando date. Se a hora for +/-15 minutos da hora atual, a integração falhará. Para corrigir esse problema, atualize a data e/ou o fuso horário do servidor Linux. 
2. Verifique se que você instalou a versão mais recente do agente do Log Analytics para Linux.  A versão mais recente agora notifica se a distorção de horário está causando a falha de integração.
3. Reintegração usando a ID do workspace correto e a chave de workspace seguindo as instruções de instalação no início deste artigo.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Você vê um erro 404 e 500 no arquivo de log logo após a integração
Esse é um problema conhecido que ocorre durante o primeiro upload de dados do Linux em um espaço de trabalho do Log Analytics. Isso não afeta os dados sendo enviados ou a experiência do serviço.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problema: você vê omiagent usando a CPU de 100%

### <a name="probable-causes"></a>Causas prováveis
Uma regressão no pacote do NSS-PEM [v 1.0.3 -5. EL7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) causou um problema de desempenho grave, que temos visto muito nas distribuições RedHat/CentOS 7. x. Para saber mais sobre esse problema, verifique a seguinte documentação: bug [1667121 regressão de desempenho em libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Os bugs relacionados ao desempenho não acontecem o tempo todo e são muito difíceis de reproduzir. Se você tiver esse problema com o omiagent, deverá usar o script omiHighCPUDiagnostics.sh, que coletará o rastreamento de pilha do omiagent ao exceder um certo limite.

1. Baixar o script <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Execute o diagnóstico por 24 horas com um limite de CPU de 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. A pilha de chamadas será despejada no arquivo omiagent_trace, se você notar muitas chamadas de função de rotação e NSS, siga as etapas de resolução abaixo.

### <a name="resolution-step-by-step"></a>Resolução (passo a passo)

1. Atualize o pacote NSS-PEM para [v 1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Se o NSS-PEM não estiver disponível para atualização (na maioria das vezes, ocorre no CentOS), faça o downgrade da ondulação para 7.29.0-46. Se, por engano, você executar "atualização yum", a ondulação será atualizada para 7.29.0-51 e o problema ocorrerá novamente. <br/>
`sudo yum downgrade curl libcurl`

3. Reiniciar OMI: <br/>
`sudo scxadmin -restart`


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problema: Você não estiver vendo mensagens do Syslog encaminhadas 

### <a name="probable-causes"></a>Causas prováveis
* A configuração aplicada ao servidor Linux não permite a coleta dos recursos e/ou dos níveis de log enviados
* O Syslog não está sendo encaminhado corretamente para o servidor Linux
* O número de mensagens que estão sendo encaminhadas por segundo é muito grande para a configuração básica do agente do Log Analytics para o Linux manipular

### <a name="resolution"></a>Resolução
* Verifique se a configuração no espaço de trabalho do Log Analytics para o Syslog tem todos os recursos e os níveis de log corretos. Revise [configurar a coleção do Syslog no portal do Azure](data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifique se os daemons do sistema de mensagens syslog nativos (`rsyslog`, `syslog-ng`) podem receber as mensagens encaminhadas
* Verifique as configurações de firewall no servidor Syslog para garantir que as mensagens não estejam sendo bloqueadas
* Simule uma mensagem do Syslog para o Log Analytics usando o comando `logger`
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problema: você está recebendo o endereço Errno já em uso no arquivo de log do agente
Se você vir `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` em omsagent.log.

### <a name="probable-causes"></a>Causas prováveis
Esse erro indica que a extensão de diagnóstico do Linux (LAD) é instalada lado a lado com a extensão de VM do Log Analytics Linux e está usando a mesma porta para a coleta de dados do syslog que o omsagent.

### <a name="resolution"></a>Resolução
1. Como root, execute os seguintes comandos (note que 25224 é um exemplo e é possível que em seu ambiente você veja um número de porta diferente usado pelo LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Em seguida, você precisa editar o arquivo de configuração `rsyslogd` ou `syslog_ng` correto e alterar a configuração relacionada ao LAD para gravar na porta 25229.

2. Se a VM estiver executando `rsyslogd`, o arquivo a ser modificado será: `/etc/rsyslog.d/95-omsagent.conf` (se existir, caso contrário, `/etc/rsyslog`). Se a VM estiver em execução `syslog_ng`, o arquivo a ser modificada é: `/etc/syslog-ng/syslog-ng.conf`.
3. Reinicie o omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Reinicie o serviço de syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problema: você não consegue desinstalar o omsagent usando a opção de limpeza

### <a name="probable-causes"></a>Causas prováveis

* A extensão de diagnóstico do Linux está instalada
* O Linux Diagnostic Extension foi instalado e desinstalado, mas você ainda vê um erro sobre o uso do agente e não pode ser removido.

### <a name="resolution"></a>Resolução
1. Desinstale a extensão de diagnóstico do Linux (LAD).
2. Remova os arquivos do Linux Diagnostic Extension da máquina, se estiverem presentes no seguinte local: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` e `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problema: não é possível ver dados em nenhum dado do Nagios 

### <a name="probable-causes"></a>Causas prováveis
* Omsagent usuário não tem permissões para ler do arquivo de log do Nagios
* A origem e o filtro do Nagios não foram descomentados do arquivo omsagent.conf

### <a name="resolution"></a>Resolução
1. Adicione o usuário omsagent para ler o arquivo Nagios seguindo estas [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. No arquivo de configuração geral do agente do Log Analytics para Linux em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, assegure-se de que **ambos** fonte e filtro do Nagios não estejam comentados.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problema: Você não está vendo nenhum dado do Linux 

### <a name="probable-causes"></a>Causas prováveis
* Falha na integração ao Azure Monitor
* A conexão com o Azure Monitor está bloqueada
* Máquina virtual foi reinicializada.
* O pacote OMI foi atualizado manualmente para uma versão mais recente em comparação com o que foi instalado pelo agente Log Analytics para o pacote Linux
* OMI está congelado, bloqueando o agente do OMS
* DSC resource logs *classe não encontrada* erro no `omsconfig.log` arquivo de log
* O agente do Log Analytics para dados é submetido a backup
* A configuração atual dos logs de DSC *não existe. Execute Start-DscConfiguration comando com o parâmetro-Path para especificar um arquivo de configuração e criar uma configuração atual primeiro.* no arquivo de log `omsconfig.log`, mas nenhuma mensagem de log existe sobre `PerformRequiredConfigurationChecks` operações.

### <a name="resolution"></a>Resolução
1. Instale todas as dependências como o pacote auditd.
2. Verifique se a integração com Azure Monitor foi bem-sucedida verificando se o seguinte arquivo existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` .  Se não foi, re-board usando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) da linha de comando omsadmin.sh.
4. Se estiver usando um proxy, verifique as etapas de solução de problemas de proxy acima.
5. Em alguns sistemas de distribuição do Azure, o daemon do servidor OMI omídico não é iniciado após a reinicialização da máquina virtual. Isso resultará na não visualização de dados relacionados à solução Audit, ChangeTracking ou UpdateManagement. A solução alternativa é iniciar manualmente o omi server executando `sudo /opt/omi/bin/service_control restart`.
6. Depois que o pacote OMI é atualizado manualmente para uma versão mais recente, ele deve ser reiniciado manualmente para que o agente do Log Analytics continue funcionando. Esta etapa é necessária para algumas distribuições em que o servidor OMI não inicia automaticamente após a atualização. Executar `sudo /opt/omi/bin/service_control restart` reiniciar OMI.
* Em algumas situações, o OMI pode se tornar congelado. O agente do OMS pode inserir um estado bloqueado aguardando OMI, bloqueando todas as coletas de dados. O processo do agente do OMS será executado, mas não haverá nenhuma atividade, evidências de nenhuma nova linha de log (como pulsações enviadas) presente em `omsagent.log` . Reinicie o OMI com `sudo /opt/omi/bin/service_control restart` para recuperar o agente.
7. Se você vir a classe do recurso DSC *não localizada* no omsconfig.log, execute `sudo /opt/omi/bin/service_control restart`.
8. Em alguns casos, quando o agente de Log Analytics para Linux não pode se comunicar com Azure Monitor, é feito backup dos dados no agente em todo o tamanho do buffer: 50 MB. O agente deve ser reiniciado executando o seguinte comando `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Esse problema foi corrigido na versão do Agente 1.1.0-28 ou posterior
    >

* Se `omsconfig.log` arquivo de log não indicar que `PerformRequiredConfigurationChecks` operações estão sendo executadas periodicamente no sistema, pode haver um problema com o trabalho / serviço cron. Certifique-se de que o trabalho cron existe em `/etc/cron.d/OMSConsistencyInvoker`. Se necessário, execute os seguintes comandos para criar a tarefa cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Além disso, verifique se que o serviço de cron está em execução. Você pode usar `service cron status` com Debian, Ubuntu, SUSE ou `service crond status` com RHEL, CentOS, Oracle Linux para verificar o status deste serviço. Se o serviço não existir, você poderá instalar os binários e iniciar o serviço usando o seguinte:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problema: Ao configurar a coleta no portal para contadores de desempenho do Syslog ou do Linux, as configurações não são aplicadas

### <a name="probable-causes"></a>Causas prováveis
* O agente do Log Analytics para Linux não selecionou a configuração mais recente
* As configurações alteradas no portal não foram aplicadas

### <a name="resolution"></a>Resolução
**Background:** `omsconfig` é o agente do Log Analytics para o agente de configuração do Linux que procura nova configuração do lado do portal a cada cinco minutos. Essa configuração é então aplicada ao agente do Log Analytics para arquivos de configuração do Linux localizados em /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* Em alguns casos, o agente de configuração do Log Analytics Agent for Linux pode não conseguir se comunicar com o serviço de configuração do portal, fazendo com que a configuração mais recente não seja aplicada.
  1. Verifique se o agente `omsconfig` está instalado executando `dpkg --list omsconfig` ou `rpm -qi omsconfig`.  Se não estiver instalado, reinstale a versão mais recente do agente Log Analytics para Linux.

  2. Verifique se o `omsconfig` agente pode se comunicar com Azure monitor executando o comando a seguir `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` . Esse comando retorna a configuração que o agente recebe do serviço, incluindo configurações de Syslog, contadores de desempenho do Linux e logs personalizados. Se este comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Esse comando força o agente programa omsconfig a se comunicar com Azure Monitor e recuperar a configuração mais recente.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problema: Você não está vendo os dados de log personalizados 

### <a name="probable-causes"></a>Causas prováveis
* Falha na integração ao Azure Monitor.
* A configuração **aplicar a configuração a seguir aos meus servidores Linux** não tiver sido selecionada.
* O omsconfig não selecionou a configuração de log customizada mais recente do serviço.
* O agente do Log Analytics para o usuário do Linux `omsagent` não pode acessar o log personalizado devido a permissões ou não encontrado.  Você pode ver os seguintes erros:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Problema conhecido com a condição de corrida corrigida no agente do Log Analytics para Linux versão 1.1.0-217

### <a name="resolution"></a>Resolução
1. Verifique se a integração com Azure Monitor foi bem-sucedida verificando se o seguinte arquivo existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` . Caso não esteja, tente:  

  1. Reintegrar usando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) da linha de comando omsadmin.sh
  2. Em **Configurações avançadas** no portal do Azure, verifique se a configuração **Aplicar a seguinte configuração aos meus servidores Linux** está habilitada.  

2. Verifique se o `omsconfig` agente pode se comunicar com Azure monitor executando o comando a seguir `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` .  Esse comando retorna a configuração que o agente recebe do serviço, incluindo configurações de Syslog, contadores de desempenho do Linux e logs personalizados. Se este comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Esse comando força o agente programa omsconfig a se comunicar com Azure Monitor e recuperar a configuração mais recente.

**Background:** Em vez do agente do Log Analytics para Linux sendo executado como um usuário privilegiado - `root`, o agente é executado como o usuário `omsagent`. Na maioria dos casos, a permissão explícita deve ser concedida a esse usuário para que determinados arquivos sejam lidos. Para conceder permissão para o usuário `omsagent`, execute os seguintes comandos:

1. Adicione o usuário `omsagent` ao grupo específico `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Conceda acesso de leitura universal para o arquivo necessário `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Há um problema conhecido com uma condição de corrida com o agente do Log Analytics para a versão do Linux anterior a 1.1.0-217. Após atualizar para o agente mais recente, execute o seguinte comando para obter a versão mais recente do plug-in de saída `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problema: você está tentando reingressar em um novo workspace
Quando você tenta re-inserir um agente em um novo workspace, a configuração do agente do Log Analytics precisa ser limpa antes de reenquadrar. Para limpar a configuração antiga do agente, execute o pacote do shell com `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Ou

```
sudo sh ./onboard_agent.sh --purge
```

Você pode continuar reonboard depois de usar a opção `--purge`

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>A extensão do agente do Log Analytics no portal do Azure é marcada com um estado de falha: Falha no provisionamento

### <a name="probable-causes"></a>Causas prováveis
* O agente do Log Analytics foi removido do sistema operacional
* O agente do Log Analytics está desabilitado, desativado ou não foi configurado

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.
1. Remova a extensão do portal do Azure.
2. Instale o agente seguindo as [instruções](../vm/quick-collect-linux-computer.md).
3. Reinicie o agente executando o seguinte comando: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Aguarde alguns minutos e o estado de aprovisionamento muda para **Provisioning sucedeu**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problema: a atualização do agente do Log Analytics sob demanda

### <a name="probable-causes"></a>Causas prováveis

Os pacotes do agente do Log Analytics no host estão desatualizados.

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.

1. Verificar a versão mais recente em [página](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Baixe o script de instalação (1.4.2-124 como versão de exemplo):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Atualizar pacotes executando `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.

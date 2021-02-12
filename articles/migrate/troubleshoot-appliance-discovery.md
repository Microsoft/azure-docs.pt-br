---
title: Solucionar problemas de implantação e descoberta do dispositivo de migração do Azure
description: Obtenha ajuda com a implantação de dispositivo e a descoberta de máquina.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 810ea58c5d88dec53463b9a2b04750169c70e137
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704020"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Solucionar problemas do dispositivo e da descoberta de migrações para Azure

Este artigo ajuda você a solucionar problemas ao implantar o dispositivo de [migrações para Azure](migrate-services-overview.md) e usar o dispositivo para descobrir computadores locais.


## <a name="whats-supported"></a>Para que há suporte?

[Examine](migrate-appliance.md) os requisitos de suporte do dispositivo.


## <a name="invalid-ovf-manifest-entry"></a>"Entrada de manifesto OVF inválida"

Se você receber o erro "o arquivo de manifesto fornecido é inválido: entrada de manifesto OVF inválida", faça o seguinte:

1. Verifique se o arquivo OVA do dispositivo de migração do Azure foi baixado corretamente verificando seu valor de hash. [Saiba mais](./tutorial-discover-vmware.md). Se o valor de hash não corresponder, baixe o arquivo OVA novamente e repita a implantação.
2. Se a implantação ainda falhar e você estiver usando o VMware vSphere cliente para implantar o arquivo OVF, tente implantá-lo por meio do cliente Web vSphere. Se a implantação ainda falhar, tente usar um navegador da Web diferente.
3. Se você estiver usando o cliente Web vSphere e tentando implantá-lo no vCenter Server 6,5 ou 6,7, tente implantar o OVA diretamente no host ESXi:
   - Conecte-se diretamente ao host ESXi (em vez de vCenter Server) com o cliente Web (https://<*endereço IP do host*>/UI).
   - Em   >  **inventário** doméstico, selecione **arquivo**  >  **implantar modelo de OVF**. Navegue até o OVA e conclua a implantação.
4. Se a implantação ainda falhar, entre em contato com o suporte do Migrações para Azure.

## <a name="cant-connect-to-the-internet"></a>Não é possível conectar à Internet

Isso pode acontecer se a máquina do dispositivo estiver atrás de um proxy.

- Certifique-se de fornecer as credenciais de autorização se o proxy precisar delas.
- Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, adicione [essas URLs](migrate-appliance.md#url-access) a uma lista de permissões.
- Se você estiver usando um proxy de interceptação para se conectar à Internet, importe o certificado de proxy para a VM do dispositivo usando [estas etapas](./migrate-appliance.md).


## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Não é possível entrar no Azure por meio do aplicativo Web do dispositivo

O erro "Desculpe, mas estamos com problemas para entrar" é exibido se você estiver usando a conta do Azure incorreta para entrar no Azure. Esse erro ocorre por alguns motivos:

- Se você entrar no aplicativo Web do dispositivo para a nuvem pública, usando as credenciais da conta de usuário para o portal de nuvem do governo.
- Se você entrar no aplicativo Web do dispositivo para a nuvem governamental usando as credenciais da conta de usuário para o portal de nuvem privada.

Verifique se você está usando as credenciais corretas.

##  <a name="datetime-synchronization-error"></a>Erro de sincronização de data/hora

Um erro sobre a sincronização de data e hora (802) indica que o relógio do servidor pode estar fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio na VM do coletor para corresponder à hora atual:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute **w32tm/TZ**.
3. Para sincronizar o tempo, execute **w32tm/resync**.


## <a name="unabletoconnecttoserver"></a>UnableToConnectToServer

Se você receber esse erro de conexão, talvez não consiga se conectar ao vCenter Server *ServerName*. com: 9443. Os detalhes do erro indicam que não há nenhum ponto de extremidade ouvindo no `https://\*servername*.com:9443/sdk` que possa aceitar a mensagem.

- Verifique se você está executando a versão mais recente do dispositivo. Se você não tiver, atualize o dispositivo para a [versão mais recente](./migrate-appliance.md).
- Se o problema ainda ocorrer na versão mais recente, o dispositivo poderá não conseguir resolver o nome de vCenter Server especificado ou a porta especificada poderá estar errada. Por padrão, se a porta não for especificada, o coletor tentará se conectar ao número da porta 443.

    1. Execute o ping *ServerName*. com do dispositivo.
    2. Se a etapa 1 falhar, tente se conectar ao servidor do vCenter usando o endereço IP.
    3. Identifique o número da porta correto para se conectar ao vCenter Server.
    4. Verifique se vCenter Server está em execução.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Erro 60052/60039: o dispositivo pode não estar registrado

- O erro 60052, "o dispositivo pode não estar registrado com êxito no projeto de migrações para Azure" ocorrerá se a conta do Azure usada para registrar o dispositivo não tiver permissões suficientes.
    - Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura.
    - [Saiba mais](./migrate-appliance.md#appliance---vmware) sobre as funções e permissões necessárias do Azure.
- O erro 60039, "o dispositivo pode não estar registrado com êxito no projeto de migrações para Azure" pode ocorrer se o registro falhar porque o projeto de migrações para Azure usado para registrar o dispositivo não foi encontrado.
    - No portal do Azure e verifique se o projeto existe no grupo de recursos.
    - Se o projeto não existir, crie um novo projeto de migrações para Azure em seu grupo de recursos e registre o dispositivo novamente. [Saiba como](./create-manage-projects.md#create-a-project-for-the-first-time) criar um novo projeto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Erro 60030/60031: falha na operação de gerenciamento de Key Vault

Se você receber o erro 60030 ou 60031, "falha em uma operação de gerenciamento de Azure Key Vault", faça o seguinte:
- Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura.
- Verifique se a conta tem acesso ao cofre de chaves especificado na mensagem de erro e repita a operação.
- Se o problema persistir, contate o Suporte da Microsoft.
- [Saiba mais](./migrate-appliance.md#appliance---vmware) sobre as funções e permissões do Azure necessárias.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erro 60028: não foi possível iniciar a descoberta

Erro 60028: "não foi possível iniciar a descoberta devido a um erro. A operação falhou para a lista especificada de hosts ou clusters "indica que a descoberta não pôde ser iniciada nos hosts listados no erro devido a um problema no acesso ou na recuperação de informações da VM. O restante dos hosts foi adicionado com êxito.

- Adicione os hosts listados no erro novamente, usando a opção **Adicionar host** .
- Se houver um erro de validação, examine as diretrizes de correção para corrigir os erros e tente a opção **salvar e iniciar descoberta** novamente.

## <a name="error-60025-azure-ad-operation-failed"></a>Erro 60025: falha na operação do Azure AD 
Erro 60025: "falha em uma operação do Azure AD. O erro ocorreu ao criar ou atualizar o aplicativo do Azure AD "ocorre quando a conta de usuário do Azure usada para iniciar a descoberta é diferente da conta usada para registrar o dispositivo. Realize um dos seguintes procedimentos:

- Verifique se a conta de usuário que está iniciando a descoberta é a mesma usada para registrar o dispositivo.
- Forneça Azure Active Directory permissões de acesso do aplicativo à conta de usuário para a qual a operação de descoberta está falhando.
- Exclua o grupo de recursos criado anteriormente para o projeto de migrações para Azure. Crie outro grupo de recursos para iniciar novamente.
- [Saiba mais](./migrate-appliance.md#appliance---vmware) sobre as permissões de aplicativo Azure Active Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Erro 50004: não é possível conectar-se ao host ou cluster

Erro 50004: "não é possível se conectar a um host ou cluster porque o nome do servidor não pode ser resolvido. Código de erro WinRM: 0x803381B9 "poderá ocorrer se o serviço DNS do Azure para o dispositivo não puder resolver o cluster ou o nome do host fornecido.

- Se você vir esse erro no cluster, o FQDN do cluster.
- Você também pode ver esse erro para hosts em um cluster. Isso indica que o dispositivo pode se conectar ao cluster, mas o cluster retorna nomes de host que não são FQDNs. Para resolver esse erro, atualize o arquivo de hosts no dispositivo adicionando um mapeamento do endereço IP e nomes de host:
    1. Abra o bloco de notas como administrador.
    2. Abra o arquivo C:\Windows\System32\Drivers\etc\hosts.
    3. Adicione o endereço IP e o nome do host em uma linha. Repita para cada host ou cluster em que você vê esse erro.
    4. Salve e feche o arquivo de hosts.
    5. Verifique se o dispositivo pode se conectar aos hosts, usando o aplicativo de gerenciamento de dispositivo. Após 30 minutos, você deverá ver as informações mais recentes para esses hosts na portal do Azure.


## <a name="error-60001-unable-to-connect-to-server"></a>Erro 60001: não é possível conectar ao servidor 

- Verifique se há conectividade do dispositivo com o servidor
- Se for um servidor Linux, verifique se a autenticação baseada em senha está habilitada usando as seguintes etapas:
    1. Faça logon no computador Linux e abra o arquivo de configuração do SSH usando o comando "vi/etc/ssh/sshd_config"
    2. Defina a opção "PasswordAuthentication" como Sim. Salve o arquivo.
    3. Reinicie o serviço SSH executando "Service sshd restart"
- Se for um servidor Windows, verifique se a porta 5985 está aberta para permitir chamadas WMI remotas.
- Se você estiver descobrindo um servidor Linux GCP e usando um usuário raiz, use os comandos a seguir para alterar a configuração padrão para logon raiz
    1. Faça logon no computador Linux e abra o arquivo de configuração do SSH usando o comando "vi/etc/ssh/sshd_config"
    2. Defina a opção "PermitRootLogin" como Sim.
    3. Reinicie o serviço SSH executando "Service sshd restart"

## <a name="error-no-suitable-authentication-method-found"></a>Erro: nenhum método de autenticação adequado encontrado

Verifique se a autenticação baseada em senha está habilitada no servidor Linux usando as seguintes etapas:
    1. Faça logon no computador Linux e abra o arquivo de configuração do SSH usando o comando "vi/etc/ssh/sshd_config"
    2. Defina a opção "PasswordAuthentication" como Sim. Salve o arquivo.
    3. Reinicie o serviço SSH executando "Service sshd restart"


## <a name="discovered-vms-not-in-portal"></a>VMs descobertas não estão no portal

Se o estado da descoberta for "descoberta em andamento", mas ainda não estiver vendo as VMs no portal, aguarde alguns minutos:
- Leva cerca de 15 minutos para uma VM VMware.
- Leva cerca de dois minutos para cada host adicionado para a descoberta de VM do Hyper-V.

Se você aguardar e o estado não for alterado, selecione **Atualizar** na guia **servidores** . Isso deve mostrar a contagem dos servidores descobertos em migrações para Azure: avaliação de servidor e migrações para Azure: migração de servidor.

Se isso não funcionar e você estiver descobrindo servidores VMware:

- Verifique se a conta do vCenter especificada tem permissões definidas corretamente, com acesso a pelo menos uma VM.
- As migrações para Azure não podem descobrir VMs do VMware se a conta do vCenter tiver acesso concedido no nível da pasta da VM do vCenter. [Saiba mais](set-discovery-scope.md) sobre a descoberta de escopo.

## <a name="vm-data-not-in-portal"></a>Dados da VM não estão no portal

Se as VMs descobertas não aparecerem no portal ou se os dados da VM estiverem desatualizados, aguarde alguns minutos. Demora até 30 minutos para que as alterações nos dados de configuração da VM descobertas sejam exibidas no Portal. Pode levar algumas horas para que as alterações nos dados do aplicativo sejam exibidas. Se não houver dados após esse período, tente atualizar, da seguinte maneira

1. Em **servidores**  >  **migração do Azure migrar servidor**, selecione **visão geral**.
2. Em **gerenciar**, selecione **integridade do agente**.
3. Selecione **Atualizar agente**.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas.

## <a name="deleted-vms-appear-in-portal"></a>VMs excluídas aparecem no portal

Se você excluir VMs e elas ainda aparecerem no portal, aguarde 30 minutos. Se eles ainda aparecerem, atualize conforme descrito acima.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Não vejo dados de desempenho para alguns adaptadores de rede em meus servidores físicos

Isso pode acontecer se o servidor físico tiver a virtualização do Hyper-V habilitada. Devido a uma lacuna do produto, a taxa de transferência da rede é capturada nos adaptadores de rede virtual descobertos.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Erro: O arquivo carregado não está no formato esperado
Algumas ferramentas têm configurações regionais que criam o arquivo CSV com ponto e vírgula como delimitador. Altere as configurações para garantir que o delimitador seja uma vírgula.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Eu importei um CSV, mas vejo "A descoberta está em andamento"
Esse status será exibido se o upload do CSV falhar devido a uma falha de validação. Tente importar o CSV novamente. Você pode baixar o relatório de erros do upload anterior e seguir as orientações de correção no arquivo, para corrigir os erros. O relatório de erros pode ser baixado na seção 'Importar detalhes' na página 'Descobrir computadores'.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Não ver detalhes do aplicativo mesmo após atualizar credenciais de convidado
A descoberta de aplicativos é executada uma vez a cada 24 horas. Se você quiser ver os detalhes imediatamente, atualize da seguinte maneira. Isso pode levar alguns minutos, dependendo do número de de VMs descobertas.

1. Em **servidores**  >  **migração do Azure migrar servidor**, selecione **visão geral**.
2. Em **gerenciar**, selecione **integridade do agente**.
3. Selecione **Atualizar agente**.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas.

## <a name="unable-to-export-application-inventory"></a>Não é possível exportar o inventário de aplicativos
Verifique se o usuário que está baixando o inventário do portal tem privilégios de colaborador na assinatura.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Nenhum método de autenticação adequado encontrado para concluir a autenticação (PublicKey)
A autenticação baseada em chave não funcionará, use a autenticação de senha.

## <a name="common-app-discovery-errors"></a>Erros comuns do App Discovery

As migrações para Azure dão suporte à descoberta de aplicativos, funções e recursos usando migrações para Azure: avaliação do servidor. Atualmente, o app Discovery tem suporte apenas para VMware. [Saiba mais](how-to-discover-applications.md) sobre os requisitos e as etapas para configurar a descoberta de aplicativos.

Erros típicos de descoberta de aplicativo são resumidos na tabela. 

| **Erro** | **Causa** | **Ação** |
|--|--|--|
| 9000: não é possível detectar o status da ferramenta VMware. | As ferramentas do VMWare podem não estar instaladas ou corrompidas. | Verifique se as ferramentas do VMware estão instaladas e em execução na VM. |
| 9001: as ferramentas do VMware não estão instaladas. | As ferramentas do VMWare podem não estar instaladas ou corrompidas. | Verifique se as ferramentas do VMware estão instaladas e em execução na VM. |
| 9002: as ferramentas do VMware não estão em execução. | As ferramentas do VMWare podem não estar instaladas ou corrompidas. | Verifique se as ferramentas do VMware estão instaladas e em execução na VM. |
| 9003: não há suporte para o tipo de sistema operacional para descoberta de VM convidada. | O sistema operacional em execução no servidor não é o Windows nem o Linux. | Os tipos de sistema operacional com suporte são somente Windows e Linux. Se o servidor for, de fato, Windows ou Linux, verifique o tipo de sistema operacional especificado em vCenter Server. |
| 9004: a VM não está em execução. | A VM está desligada. | Verifique se a VM está ligada. |
| 9005: não há suporte para o tipo de sistema operacional para descoberta de VM convidada. | Tipo de sistema operacional sem suporte para descoberta de VM convidada. | Os tipos de sistema operacional com suporte são somente Windows e Linux. |
| 9006: a URL para baixar o arquivo de metadados do convidado está vazia. | Isso pode acontecer se o agente de descoberta não estiver funcionando conforme o esperado. | O problema deve resolver automaticamente In24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9007: o processo que executa a tarefa de descoberta na VM convidada não foi encontrado. | Isso pode acontecer se o agente de descoberta não estiver funcionando corretamente. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9008: o status do processo da VM convidada não pode ser recuperado. | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9009: o UAC do Windows impediu a execução da tarefa de descoberta no servidor. | As configurações de UAC (controle de conta de usuário) do Windows no servidor são restritivas e impedem a descoberta de aplicativos instalados. | Em configurações de ' controle de conta de usuário ' no servidor, defina a configuração do UAC como em um dos dois níveis inferiores. |
| 9010: a VM está desligada. | A VM está desligada. | Verifique se a VM está ligada. |
| 9011: arquivo de metadados descoberto não encontrado no sistema de arquivos da VM convidada. | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9012: o arquivo de metadados descoberto está vazio. | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9013: um novo perfil temporário é criado para cada logon. | Um novo perfil temporário é criado para cada logon na VM do VMware. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9014: não é possível recuperar metadados do sistema de arquivos da VM convidada. | Sem conectividade com o host ESXi | Verifique se o dispositivo pode se conectar à porta 443 no host ESXi que executa a VM |
| 9015: a função de operações de convidado não está habilitada na conta de usuário do vCenter | A função de operações de convidado não está habilitada na conta de usuário do vCenter. | Verifique se a função de operações de convidado está habilitada na conta de usuário do vCenter. |
| 9016: não é possível descobrir porque o agente de operações de convidado está desatualizado. | As ferramentas do VMware não estão instaladas corretamente ou não estão atualizadas. | Verifique se as ferramentas do VMware estão corretamente instaladas e atualizadas. |
| 9017: o arquivo com metadados descobertos não foi encontrado na VM. | O problema pode ocorrer devido a um erro interno. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9018: o PowerShell não está instalado nas VMs convidadas. | O PowerShell não está disponível na VM convidada. | Instale o PowerShell na VM convidada. |
| 9019: não é possível descobrir devido a falhas de operação da VM convidada. | Falha na operação de convidado do VMware na VM. | Verifique se as credenciais da VM são válidas e se o nome de usuário fornecido nas credenciais da VM convidada está no formato UPN. |
| 9020: a permissão de criação de arquivo foi negada. | A função associada ao usuário ou à política de grupo é restringir o usuário de criar o arquivo na pasta | Verifique se o usuário convidado fornecido tem permissão de criação para o arquivo na pasta. Consulte **notificações** na avaliação do servidor para o nome da pasta. |
| 9021: não é possível criar o arquivo no caminho temporário do sistema. | A ferramenta VMware relata o caminho temporário do sistema em vez do caminho temporário dos usuários. | Atualize sua versão da ferramenta VMware acima 10287 (formato de cliente do NGC/VI). |
| 9022: o acesso ao objeto WMI foi negado. | A função associada ao usuário ou à política de grupo é restringir o acesso do usuário ao objeto WMI. | Entre em contato com o Suporte da Microsoft. |
| 9023: não é possível executar o PowerShell, pois o valor da variável de ambiente SystemRoot está vazio. | O valor da variável de ambiente SystemRoot está vazio para a VM convidada. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9024: não é possível descobrir porque o valor da variável de ambiente TEMP está vazio. | O valor da variável de ambiente TEMP está vazio para a VM convidada. | Entre em contato com o Suporte da Microsoft. |
| 9025: o PowerShell está corrompido nas VMs convidadas. | O PowerShell está corrompido na VM convidada. | Reinstale o PowerShell na VM convidada e verifique se o PowerShell pode ser executado na VM convidada. |
| 9026: não é possível executar operações de convidado na VM. | O estado da VM não permite que operações de convidado sejam executadas na VM. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9027: o agente de operações de convidado não está em execução na VM. | Falha ao contatar o agente de operações convidadas em execução na máquina virtual. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9028: o arquivo não pode ser criado devido a armazenamento em disco insuficiente na VM. | Não há espaço suficiente no disco. | Verifique se há espaço suficiente disponível no armazenamento em disco da VM. |
| 9029: nenhum acesso ao PowerShell na credencial de VM convidada fornecida. | O acesso ao PowerShell não está disponível para o usuário. | Verifique se o usuário adicionado ao dispositivo pode acessar o PowerShell na VM convidada. |
| 9030: não é possível coletar metadados descobertos, pois o host ESXi está desconectado. | O host ESXi está em um estado desconectado. | Verifique se o host ESXi que executa a VM está conectado. |
| 9031: não é possível coletar metadados descobertos, pois o host ESXi não está respondendo. | O host remoto está em estado inválido. | Verifique se o host ESXi que executa a VM está em execução e conectado. |
| 9032: não é possível descobrir devido a um erro interno. | O problema pode ocorrer devido a um erro interno. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9033: não é possível descobrir, pois o nome de usuário da VM contém caracteres inválidos. | Caracteres inválidos foram detectados no nome de usuário. | Forneça novamente a credencial de VM, garantindo que não há caracteres inválidos. |
| 9034: o nome de usuário fornecido não está no formato UPN. | O nome de usuário não está no formato UPN. | Verifique se o nome de usuário está no formato de nome UPN. |
| 9035: não é possível descobrir porque o modo de linguagem do PowerShell não está definido como ' idioma completo '. | O modo de linguagem do PowerShell na VM convidada não está definido como idioma completo. | Verifique se o modo de linguagem do PowerShell está definido como ' idioma completo '. |
| 9037: a coleta de dados foi pausada temporariamente, pois o tempo de resposta da VM é muito alto. | A VM descoberta está demorando muito para responder | Nenhuma ação é necessária. Uma nova tentativa será tentada em 24 horas para a descoberta de aplicativos e 3 horas para análise de dependência (sem agente). |
| 10000: não há suporte para o tipo de sistema operacional. | O sistema operacional em execução no servidor não é o Windows nem o Linux. | Os tipos de sistema operacional com suporte são somente Windows e Linux. |
| 10001: o script para a descoberta do servidor não foi encontrado no dispositivo. | A descoberta não está funcionando conforme o esperado. | Contate a Suporte da Microsoft para obter uma resolução. |
| 10002: a tarefa de descoberta não foi concluída no tempo. | O agente de descoberta não está funcionando conforme o esperado. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 10003: o processo que executa a tarefa de descoberta foi encerrado com um erro. | Processo que executa a tarefa de descoberta foi encerrado com um erro. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema ainda persistir, entre em contato com Suporte da Microsoft. |
| 10004: credencial não fornecida para o tipo de sistema operacional convidado. | As credenciais para acessar computadores desse tipo de sistema operacional não foram fornecidas no dispositivo de migrações para Azure. | Adicionar credenciais para computadores no dispositivo |
| 10005: as credenciais fornecidas não são válidas. | As credenciais fornecidas para o dispositivo acessar o servidor estão incorretas. | Atualize as credenciais fornecidas no dispositivo e verifique se o servidor está acessível usando as credenciais. |
| 10006: tipo de SO convidado sem suporte pelo repositório de credenciais. | O sistema operacional em execução no servidor não é o Windows nem o Linux. | Os tipos de sistema operacional com suporte são somente Windows e Linux. |
| 10007: não é possível processar os metadados descobertos. | Erro ao tentar desserializar o JSON. | Contate a Suporte da Microsoft para obter uma resolução. |
| 10008: não é possível criar um arquivo no servidor. | O problema pode ocorrer devido a um erro interno. | Contate a Suporte da Microsoft para obter uma resolução. |
| 10009: não é possível gravar metadados descobertos em um arquivo no servidor. | O problema pode ocorrer devido a um erro interno. | Contate a Suporte da Microsoft para obter uma resolução. |

## <a name="next-steps"></a>Próximas etapas

Configure um dispositivo para [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)ou [servidores físicos](how-to-set-up-appliance-physical.md).
---
title: Solucionar problemas de implantação e descoberta do dispositivo de migração do Azure
description: Obtenha ajuda com a implantação de dispositivo e a descoberta de servidor.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 995914fab0e7112327ebf6ab8e32fb67181f481e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608911"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Solucionar problemas do dispositivo e da descoberta de migrações para Azure

Este artigo ajuda você a solucionar problemas ao implantar o dispositivo de [migrações para Azure](migrate-services-overview.md) e usar o dispositivo para descobrir servidores locais.

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

Isso pode acontecer se o servidor do dispositivo estiver protegido por um proxy.

- Certifique-se de fornecer as credenciais de autorização se o proxy precisar delas.
- Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, adicione [essas URLs](migrate-appliance.md#url-access) a uma permissão.
- Se você estiver usando um proxy de interceptação para se conectar à Internet, importe o certificado de proxy para o dispositivo usando [estas etapas](./migrate-appliance.md).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Não é possível entrar no Azure por meio do aplicativo Web do dispositivo

O erro "Desculpe, mas estamos com problemas para entrar" é exibido se você estiver usando a conta do Azure incorreta para entrar no Azure. Esse erro ocorre por alguns motivos:

- Se você entrar no aplicativo Web do dispositivo para a nuvem pública, usando as credenciais da conta de usuário para o portal de nuvem do governo.
- Se você entrar no aplicativo Web do dispositivo para a nuvem governamental usando as credenciais da conta de usuário para o portal de nuvem privada.

Verifique se você está usando as credenciais corretas.

## <a name="datetime-synchronization-error"></a>Erro de sincronização de data/hora

Um erro sobre a sincronização de data e hora (802) indica que o relógio do servidor pode estar fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio no servidor do coletor para corresponder à hora atual:

1. Abra um prompt de comando de administrador no servidor.
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

- Erro 60052, "o dispositivo pode não estar registrado com êxito no projeto" ocorre se a conta do Azure usada para registrar o dispositivo não tiver permissões suficientes.
    - Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura.
    - [Saiba mais](./migrate-appliance.md#appliance---vmware) sobre as funções e permissões necessárias do Azure.
- Erro 60039, "o dispositivo pode não estar registrado com êxito no projeto" pode ocorrer se o registro falhar porque o projeto usado para registrar o dispositivo não pode ser encontrado.
    - No portal do Azure e verifique se o projeto existe no grupo de recursos.
    - Se o projeto não existir, crie um novo projeto em seu grupo de recursos e registre o dispositivo novamente. [Saiba como](./create-manage-projects.md#create-a-project-for-the-first-time) criar um novo projeto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Erro 60030/60031: falha na operação de gerenciamento de Key Vault

Se você receber o erro 60030 ou 60031, "falha em uma operação de gerenciamento de Azure Key Vault", faça o seguinte:

- Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura.
- Verifique se a conta tem acesso ao cofre de chaves especificado na mensagem de erro e repita a operação.
- Se o problema persistir, contate o Suporte da Microsoft.
- [Saiba mais](./migrate-appliance.md#appliance---vmware) sobre as funções e permissões do Azure necessárias.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erro 60028: não foi possível iniciar a descoberta

Erro 60028: "não foi possível iniciar a descoberta devido a um erro. A operação falhou para a lista especificada de hosts ou clusters "indica que a descoberta não pôde ser iniciada nos hosts listados no erro devido a um problema no acesso ou na recuperação de informações do servidor. O restante dos hosts foi adicionado com êxito.

- Adicione os hosts listados no erro novamente, usando a opção **Adicionar host** .
- Se houver um erro de validação, examine as diretrizes de correção para corrigir os erros e tente a opção **salvar e iniciar descoberta** novamente.

## <a name="error-60025-azure-ad-operation-failed"></a>Erro 60025: falha na operação do Azure AD

Erro 60025: "falha em uma operação do Azure AD. O erro ocorreu ao criar ou atualizar o aplicativo do Azure AD "ocorre quando a conta de usuário do Azure usada para iniciar a descoberta é diferente da conta usada para registrar o dispositivo. Realize um dos seguintes procedimentos:

- Verifique se a conta de usuário que está iniciando a descoberta é a mesma usada para registrar o dispositivo.
- Forneça Azure Active Directory permissões de acesso do aplicativo à conta de usuário para a qual a operação de descoberta está falhando.
- Exclua o grupo de recursos criado anteriormente para o projeto. Crie outro grupo de recursos para iniciar novamente.
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
    1. Faça logon no servidor Linux e abra o arquivo de configuração do SSH usando o comando "vi/etc/ssh/sshd_config"
    2. Defina a opção "PasswordAuthentication" como Sim. Salve o arquivo.
    3. Reinicie o serviço SSH executando "Service sshd restart"
- Se for um servidor Windows, verifique se a porta 5985 está aberta para permitir chamadas WMI remotas.
- Se você estiver descobrindo um servidor Linux GCP e usando um usuário raiz, use os comandos a seguir para alterar a configuração padrão para logon raiz
    1. Faça logon no servidor Linux e abra o arquivo de configuração do SSH usando o comando "vi/etc/ssh/sshd_config"
    2. Defina a opção "PermitRootLogin" como Sim.
    3. Reinicie o serviço SSH executando "Service sshd restart"

## <a name="error-no-suitable-authentication-method-found"></a>Erro: nenhum método de autenticação adequado encontrado

Verifique se a autenticação baseada em senha está habilitada no servidor Linux usando as seguintes etapas:
    1. Faça logon no servidor Linux e abra o arquivo de configuração do SSH usando o comando "vi/etc/ssh/sshd_config"
    2. Defina a opção "PasswordAuthentication" como Sim. Salve o arquivo.
    3. Reinicie o serviço SSH executando "Service sshd restart"

## <a name="discovered-servers-not-in-portal"></a>Servidores descobertos não estão no portal

Se o estado da descoberta for "descoberta em andamento", mas ainda não estiver vendo os servidores no portal, aguarde alguns minutos:

- Leva cerca de 15 minutos para um servidor no VMware.
- Leva cerca de dois minutos para cada host adicionado para servidores na descoberta do Hyper-V.

Se você aguardar e o estado não for alterado, selecione **Atualizar** na guia **servidores** . Isso deve mostrar a contagem dos servidores descobertos nas migrações para Azure: descoberta e avaliação e migrações para Azure: migração de servidor.

Se isso não funcionar e você estiver descobrindo servidores VMware:

- Verifique se a conta do vCenter especificada tem permissões definidas corretamente, com acesso a pelo menos um servidor.
- As migrações para Azure não poderão descobrir servidores no VMware se a conta do vCenter tiver acesso concedido no nível da pasta da VM do vCenter. [Saiba mais](set-discovery-scope.md) sobre a descoberta de escopo.

## <a name="server-data-not-in-portal"></a>Dados do servidor não estão no portal

Se os servidores descobertos não aparecerem no portal ou se os dados do servidor estiverem desatualizados, aguarde alguns minutos. Leva até 30 minutos para que as alterações nos dados de configuração do servidor descobertos sejam exibidas no Portal. Pode levar algumas horas para que as alterações nos dados de inventário de software sejam exibidas. Se não houver dados após esse período, tente atualizar, da seguinte maneira

1. Em **servidores Windows, Linux e SQL**  >  **migrações para Azure: descoberta e avaliação**, selecione **visão geral**.
2. Em **gerenciar**, selecione **integridade do agente**.
3. Selecione **Atualizar agente**.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas.

## <a name="deleted-servers-appear-in-portal"></a>Servidores excluídos aparecem no portal

Se você excluir servidores e eles ainda aparecerem no portal, aguarde 30 minutos. Se eles ainda aparecerem, atualize conforme descrito acima.

## <a name="discovered-software-inventory-and-sql-server-instances-and-databases-not-in-portal"></a>Inventário de software descoberto e instâncias de SQL Server e bancos de dados que não estão no portal

Depois de ter iniciado a descoberta no dispositivo, pode levar até 24 horas para começar a mostrar os dados de inventário no Portal.

Se você não tiver fornecido autenticação do Windows nem SQL Server credenciais de autenticação no Gerenciador de configuração de dispositivo, adicione as credenciais para que o dispositivo possa usá-las para se conectar às respectivas instâncias de SQL Server.

Uma vez conectado, o dispositivo coleta dados de desempenho e configuração de SQL Server instâncias e bancos de dado. Os dados de configuração do SQL Server são atualizados uma vez a cada 24 horas e os dados de desempenho são capturados a cada 30 segundos. Portanto, qualquer alteração nas propriedades da SQL Server instância e dos bancos de dados, como status do banco de dados, nível de compatibilidade, etc. pode levar até 24 horas para ser atualizada no Portal.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>SQL Server instância está aparecendo no estado "não conectado" no portal

Para exibir os problemas encontrados durante a descoberta de instâncias de SQL Server e bancos de dados, clique no status "não conectado" na coluna status da conexão na página ' servidores descobertos ' em seu projeto.

Criando a avaliação sobre servidores que contêm instâncias SQL que não foram descobertas completamente ou que estão no estado não conectado, pode levar à prontidão sendo marcada como "desconhecida".

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Não vejo dados de desempenho para alguns adaptadores de rede em meus servidores físicos

Isso pode acontecer se o servidor físico tiver a virtualização do Hyper-V habilitada. Devido a uma lacuna do produto, a taxa de transferência da rede é capturada nos adaptadores de rede virtual descobertos.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Erro: O arquivo carregado não está no formato esperado

Algumas ferramentas têm configurações regionais que criam o arquivo CSV com ponto e vírgula como delimitador. Altere as configurações para garantir que o delimitador seja uma vírgula.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Eu importei um CSV, mas vejo "A descoberta está em andamento"

Esse status será exibido se o upload do CSV falhar devido a uma falha de validação. Tente importar o CSV novamente. Você pode baixar o relatório de erros do upload anterior e seguir as orientações de correção no arquivo, para corrigir os erros. O relatório de erros pode ser baixado da seção ' importar detalhes ' na página ' descobrir servidores '.

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>Não ver detalhes de inventário de software mesmo após atualizar credenciais de convidado

A descoberta de inventário de software é executada uma vez a cada 24 horas. Se você quiser ver os detalhes imediatamente, atualize da seguinte maneira. Isso pode levar alguns minutos, dependendo do número de de servidores descobertos.

1. Em **servidores Windows, Linux e SQL**  >  **migrações para Azure: descoberta e avaliação**, selecione **visão geral**.
2. Em **gerenciar**, selecione **integridade do agente**.
3. Selecione **Atualizar agente**.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas.

## <a name="unable-to-export-software-inventory"></a>Não é possível exportar o inventário de software

Verifique se o usuário que está baixando o inventário do portal tem privilégios de colaborador na assinatura.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Nenhum método de autenticação adequado encontrado para concluir a autenticação (PublicKey)

A autenticação baseada em chave não funcionará, use a autenticação de senha.

## <a name="common-app-discovery-errors"></a>Erros comuns do App Discovery

As migrações para Azure dão suporte à descoberta de inventário de software, usando migrações para Azure: descoberta e avaliação. Atualmente, o app Discovery tem suporte apenas para VMware. [Saiba mais](how-to-discover-applications.md) sobre os requisitos e as etapas para configurar a descoberta de aplicativos.

Erros típicos de descoberta de aplicativo são resumidos na tabela.

| **Erro** | **Causa** | **Ação** |
|--|--|--|
| 9000: não é possível detectar o status da ferramenta VMware. | As ferramentas do VMware podem não estar instaladas ou corrompidas. | Verifique se as ferramentas do VMware estão instaladas e em execução no servidor. |
| 9001: as ferramentas do VMware não estão instaladas. | As ferramentas do VMware podem não estar instaladas ou corrompidas. | Verifique se as ferramentas do VMware estão instaladas e em execução no servidor. |
| 9002: as ferramentas do VMware não estão em execução. | As ferramentas do VMware podem não estar instaladas ou corrompidas. | Verifique se as ferramentas do VMware estão instaladas e em execução no servidor. |
| 9003: não há suporte para o tipo de sistema operacional para descoberta de servidor convidado. | O sistema operacional em execução no servidor não é o Windows nem o Linux. | Os tipos de sistema operacional com suporte são somente Windows e Linux. Se o servidor for, de fato, Windows ou Linux, verifique o tipo de sistema operacional especificado em vCenter Server. |
| 9004: o servidor não está em execução. | O servidor está desligado. | Verifique se o servidor está ligado. |
| 9005: não há suporte para o tipo de sistema operacional para descoberta de servidor convidado. | Tipo de sistema operacional sem suporte para descoberta de servidor convidado. | Os tipos de sistema operacional com suporte são somente Windows e Linux. |
| 9006: a URL para baixar o arquivo de metadados do convidado está vazia. | Isso pode acontecer se o agente de descoberta não estiver funcionando conforme o esperado. | O problema deve resolver automaticamente In24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9007: o processo que executa a tarefa de descoberta no servidor convidado não foi encontrado. | Isso pode acontecer se o agente de descoberta não estiver funcionando corretamente. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9008: o status do processo do servidor convidado não pode ser recuperado. | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9009: o UAC do Windows impediu a execução da tarefa de descoberta no servidor. | As configurações de UAC (controle de conta de usuário) do Windows no servidor são restritivas e impedem a descoberta do inventário de software instalado. | Em configurações de ' controle de conta de usuário ' no servidor, defina a configuração do UAC como em um dos dois níveis inferiores. |
| 9010: o servidor está desligado. | O servidor está desligado. | Verifique se o servidor está ligado. |
| 9011: arquivo de metadados descoberto não encontrado no sistema de arquivos do servidor convidado. | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9012: o arquivo de metadados descoberto está vazio. | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 9013: um novo perfil temporário é criado para cada logon. | Um novo perfil temporário é criado para cada logon no servidor no VMware. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9014: não é possível recuperar metadados do sistema de arquivos do servidor convidado. | Sem conectividade com o host ESXi | Verifique se o dispositivo pode se conectar à porta 443 no host ESXi que está executando o servidor |
| 9015: a função de operações de convidado não está habilitada na conta de usuário do vCenter | A função de operações de convidado não está habilitada na conta de usuário do vCenter. | Verifique se a função de operações de convidado está habilitada na conta de usuário do vCenter. |
| 9016: não é possível descobrir porque o agente de operações de convidado está desatualizado. | As ferramentas do VMware não estão instaladas corretamente ou não estão atualizadas. | Verifique se as ferramentas do VMware estão corretamente instaladas e atualizadas. |
| 9017: o arquivo com metadados descobertos não foi encontrado no servidor. | O problema pode ocorrer devido a um erro interno. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9018: o PowerShell não está instalado nos servidores convidados. | O PowerShell não está disponível no servidor convidado. | Instale o PowerShell no servidor convidado. |
| 9019: não é possível descobrir devido a falhas de operação do servidor convidado. | Falha na operação de convidado do VMware no servidor. | Verifique se as credenciais do servidor são válidas e se o nome de usuário fornecido nas credenciais do servidor convidado está no formato UPN. |
| 9020: a permissão de criação de arquivo foi negada. | A função associada ao usuário ou à política de grupo é restringir o usuário de criar o arquivo na pasta | Verifique se o usuário convidado fornecido tem permissão de criação para o arquivo na pasta. Consulte **notificações** em migrações para Azure: descoberta e avaliação para o nome da pasta. |
| 9021: não é possível criar o arquivo no caminho temporário do sistema. | A ferramenta VMware relata o caminho temporário do sistema em vez do caminho temporário dos usuários. | Atualize sua versão da ferramenta VMware acima 10287 (formato de cliente do NGC/VI). |
| 9022: o acesso ao objeto WMI foi negado. | A função associada ao usuário ou à política de grupo é restringir o acesso do usuário ao objeto WMI. | Entre em contato com o Suporte da Microsoft. |
| 9023: não é possível executar o PowerShell, pois o valor da variável de ambiente SystemRoot está vazio. | O valor da variável de ambiente SystemRoot está vazio para o servidor convidado. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9024: não é possível descobrir porque o valor da variável de ambiente TEMP está vazio. | O valor da variável de ambiente TEMP está vazio para o servidor convidado. | Entre em contato com o Suporte da Microsoft. |
| 9025: o PowerShell está corrompido nos servidores convidados. | O PowerShell está corrompido no servidor convidado. | Reinstale o PowerShell no servidor convidado e verifique se o PowerShell pode ser executado no servidor convidado. |
| 9026: não é possível executar operações de convidado no servidor. | O estado do servidor não permite que as operações de convidado sejam executadas no servidor. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9027: o agente de operações de convidado não está em execução no servidor. | Falha ao contatar o agente de operações convidadas em execução no servidor virtual. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9028: o arquivo não pode ser criado devido a armazenamento em disco insuficiente no servidor. | Não há espaço suficiente no disco. | Verifique se há espaço suficiente disponível no armazenamento em disco do servidor. |
| 9029: nenhum acesso ao PowerShell na credencial do servidor convidado fornecida. | O acesso ao PowerShell não está disponível para o usuário. | Verifique se o usuário adicionado no dispositivo pode acessar o PowerShell no servidor convidado. |
| 9030: não é possível coletar metadados descobertos, pois o host ESXi está desconectado. | O host ESXi está em um estado desconectado. | Verifique se o host ESXi que está executando o servidor está conectado. |
| 9031: não é possível coletar metadados descobertos, pois o host ESXi não está respondendo. | O host remoto está em estado inválido. | Verifique se o host ESXi que executa o servidor está em execução e conectado. |
| 9032: não é possível descobrir devido a um erro interno. | O problema pode ocorrer devido a um erro interno. | Contate a Suporte da Microsoft para obter uma resolução. |
| 9033: não é possível descobrir porque o nome de usuário do servidor contém caracteres inválidos. | Caracteres inválidos foram detectados no nome de usuário. | Forneça novamente a credencial do servidor, garantindo que não há caracteres inválidos. |
| 9034: o nome de usuário fornecido não está no formato UPN. | O nome de usuário não está no formato UPN. | Verifique se o nome de usuário está no formato de nome UPN. |
| 9035: não é possível descobrir porque o modo de linguagem do PowerShell não está definido como ' idioma completo '. | O modo de linguagem do PowerShell no servidor convidado não está definido como idioma completo. | Verifique se o modo de linguagem do PowerShell está definido como ' idioma completo '. |
| 9037: a coleta de dados foi pausada temporariamente, pois o tempo de resposta do servidor é muito alto. | O servidor descoberto está levando muito tempo para responder | Nenhuma ação é necessária. Uma nova tentativa será tentada em 24 horas para a descoberta de inventário de software e 3 horas para análise de dependência (sem agente). |
| 10000: não há suporte para o tipo de sistema operacional. | O sistema operacional em execução no servidor não é o Windows nem o Linux. | Os tipos de sistema operacional com suporte são somente Windows e Linux. |
| 10001: o script para a descoberta do servidor não foi encontrado no dispositivo. | A descoberta não está funcionando conforme o esperado. | Contate a Suporte da Microsoft para obter uma resolução. |
| 10002: a tarefa de descoberta não foi concluída no tempo. | O agente de descoberta não está funcionando conforme o esperado. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema persistir, contate o Suporte da Microsoft. |
| 10003: o processo que executa a tarefa de descoberta foi encerrado com um erro. | Processo que executa a tarefa de descoberta foi encerrado com um erro. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema ainda persistir, entre em contato com Suporte da Microsoft. |
| 10004: credencial não fornecida para o tipo de sistema operacional convidado. | As credenciais para servidores de acesso deste tipo de sistema operacional não foram fornecidas no dispositivo de migrações para Azure. | Adicionar credenciais para servidores no dispositivo |
| 10005: as credenciais fornecidas não são válidas. | As credenciais fornecidas para o dispositivo acessar o servidor estão incorretas. | Atualize as credenciais fornecidas no dispositivo e verifique se o servidor está acessível usando as credenciais. |
| 10006: tipo de SO convidado sem suporte pelo repositório de credenciais. | O sistema operacional em execução no servidor não é o Windows nem o Linux. | Os tipos de sistema operacional com suporte são somente Windows e Linux. |
| 10007: não é possível processar os metadados descobertos. | Erro ao tentar desserializar o JSON. | Contate a Suporte da Microsoft para obter uma resolução. |
| 10008: não é possível criar um arquivo no servidor. | O problema pode ocorrer devido a um erro interno. | Contate a Suporte da Microsoft para obter uma resolução. |
| 10009: não é possível gravar metadados descobertos em um arquivo no servidor. | O problema pode ocorrer devido a um erro interno. | Contate a Suporte da Microsoft para obter uma resolução. |

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>Instâncias comuns de SQL Server e erros de descoberta de banco de dados

As migrações para Azure dão suporte à descoberta de instâncias de SQL Server e bancos de dados em execução em computadores locais, usando migrações para Azure: descoberta e avaliação. Atualmente, a descoberta SQL tem suporte apenas para VMware. Consulte o tutorial de [descoberta](tutorial-discover-vmware.md) para começar.

Erros típicos de descoberta de SQL são resumidos na tabela.

| **Erro** | **Causa** | **Ação** |
|--|--|--|
|30000: as credenciais associadas a este SQL Server não funcionaram.|As credenciais manualmente associadas são inválidas ou as credenciais associadas automaticamente não podem mais acessar o SQL Server.|Adicione credenciais para SQL Server no dispositivo e aguarde até o próximo ciclo de descoberta do SQL ou a atualização forçada.|
|30001: não é possível conectar-se a SQL Server do dispositivo.|1. o dispositivo não tem a linha de visão de rede para SQL Server.<br/>2. firewall bloqueando a conexão entre SQL Server e dispositivo.|1. torne SQL Server acessível do dispositivo.<br/>2. permitir conexões de entrada do dispositivo para SQL Server.|
|30003: o certificado não é confiável.|Um certificado confiável não está instalado no computador que executa o SQL Server.|Configure um certificado confiável no servidor. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153616)|
|30004: permissões insuficientes.|Esse erro pode ocorrer devido à falta de permissões necessárias para verificar SQL Server instâncias. |Conceda a função sysadmin às credenciais/conta fornecidas no dispositivo para descobrir SQL Server instâncias e bancos de dados. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153511)|
|30005: falha ao conectar SQL Server logon devido a um problema com seu banco de dados mestre padrão.|O próprio banco de dados é inválido ou o logon não tem a permissão CONNECT no banco de dados.|Use ALTER LOGIN para definir o banco de dados padrão como banco de dados mestre.<br/>Conceda a função sysadmin às credenciais/conta fornecidas no dispositivo para descobrir SQL Server instâncias e bancos de dados. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153615)|
|30006: o logon SQL Server não pode ser usado com a autenticação do Windows.|1. o logon pode ser um SQL Server logon, mas o servidor só aceita a autenticação do Windows.<br/>2. você está tentando se conectar usando a autenticação SQL Server, mas o logon usado não existe em SQL Server.<br/>3. o logon pode usar a autenticação do Windows, mas o logon é uma entidade de segurança do Windows não reconhecida. Uma entidade não reconhecida do Windows significa que o logon não pode ser verificado pelo Windows. Talvez isso ocorra porque o logon do Windows é de um domínio não confiável.|Se você estiver tentando se conectar usando a autenticação SQL Server, verifique se SQL Server está configurado no modo de autenticação mista e se SQL Server logon existe.<br/>Se você estiver tentando se conectar usando a Autenticação do Windows, verifique se está devidamente conectado no domínio correto. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153421)|
|30007: senha expirada.|A senha da conta expirou.|A senha de logon do SQL Server pode ter expirado, redefina a senha e/ou estenda a data de expiração da senha. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153419)|
|30008: a senha deve ser alterada.|A senha da conta precisa ser alterada.|Altere a senha da credencial fornecida para SQL Server descoberta. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153318)|
|30009: ocorreu um erro interno.|Ocorreu um erro interno ao descobrir SQL Server instâncias e bancos de dados. |Entre em contato com o suporte da Microsoft se o problema persistir.|
|30010: nenhum banco de dados encontrado.|Não é possível localizar bancos de dados da instância de servidor selecionada.|Conceda a função sysadmin às credenciais/conta fornecidas no dispositivo para descobrir bancos de dados SQL.|
|30011: ocorreu um erro interno ao avaliar uma instância ou um banco de dados SQL.|Ocorreu um erro interno ao executar a avaliação.|Entre em contato com o suporte da Microsoft se o problema persistir.|
|30012: falha na conexão SQL.|1. o firewall no servidor recusou a conexão.<br/>2. o serviço de SQL Server Browser (sqlbrowser) não foi iniciado.<br/>3. SQL Server não respondeu à solicitação do cliente porque o servidor provavelmente não foi iniciado.<br/>4. o cliente do SQL Server não pode se conectar ao servidor. Esse erro pode ocorrer porque o servidor não está configurado para aceitar conexões remotas.<br/>5. o cliente do SQL Server não pode se conectar ao servidor. Esse erro pode ocorrer porque o cliente não pode resolver o nome do servidor ou o nome do servidor está incorreto.<br/>6. os protocolos TCP ou pipe nomeado não estão habilitados.<br/>7. o nome da instância de SQL Server especificado não é válido.|Use [este](https://go.microsoft.com/fwlink/?linkid=2153317) guia do usuário interativo para solucionar o problema de conectividade. Aguarde 24 horas depois de seguir o guia para os dados a serem atualizados no serviço. Se o problema persistir, entre em contato com o suporte da Microsoft.|
|30013: ocorreu um erro ao estabelecer uma conexão com a instância do SQL Server.|1. o nome do SQL Server não pode ser resolvido do dispositivo.<br/>2. SQL Server não permite conexões remotas.|Se você puder executar o ping no SQL Server do dispositivo, Aguarde 24 horas para verificar se esse problema resolve automaticamente. Caso contrário, entre em contato com o suporte da Microsoft. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153316)|
|30014: nome de usuário ou senha inválido.| Esse erro pode ocorrer devido a uma falha de autenticação que envolve uma senha ou um nome de usuário inválidos.|Forneça uma credencial com um nome de usuário e senha válidos. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153315)|
|30015: ocorreu um erro interno ao descobrir a instância do SQL.|Ocorreu um erro interno ao descobrir a instância do SQL.|Entre em contato com o suporte da Microsoft se o problema persistir.|
|30016: a conexão à instância '% Instance; ' falhou devido a um tempo limite.| Isso pode ocorrer se o firewall no servidor recusar a conexão.|Verifique se o firewall no SQL Server está configurado para aceitar conexões. Se o erro persistir, entre em contato com o suporte da Microsoft. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2153611)|
|30017: ocorreu um erro interno.|Exceção sem tratamento.|Entre em contato com o suporte da Microsoft se o problema persistir.|
|30018: ocorreu um erro interno.|Ocorreu um erro interno ao coletar dados como tamanho do BD temporário, tamanho do arquivo, etc. da instância do SQL.|Aguarde 24 horas e entre em contato com o suporte da Microsoft se o problema persistir.|
|30019: ocorreu um erro interno.|Ocorreu um erro interno ao coletar métricas de desempenho, como utilização de memória, etc. de um banco de dados ou uma instância.|Aguarde 24 horas e entre em contato com o suporte da Microsoft se o problema persistir.|

## <a name="next-steps"></a>Próximas etapas

Configure um dispositivo para [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)ou [servidores físicos](how-to-set-up-appliance-physical.md).

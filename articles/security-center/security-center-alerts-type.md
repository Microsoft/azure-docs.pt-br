---
title: Alertas de segurança por tipo na Central de Segurança do Azure | Microsoft Docs
description: Este artigo descreve os diferentes tipos de alertas de segurança disponíveis na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: monhaber
ms.openlocfilehash: 407d0610f919b1145a91e8678f57f8d344c9c187
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916219"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Noções básicas de alertas de segurança na Central de Segurança do Azure
Este artigo ajuda você a compreender os diferentes tipos de alertas de segurança e as informações relacionadas disponíveis na Central de Segurança do Azure. Para saber mais sobre como gerenciar os alertas e os incidentes, confira [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md).

Para configurar as detecções avançadas, atualize para a Central de Segurança do Azure Standard. Há uma avaliação gratuita disponível. Para atualizar, selecione **Tipo de Preço** na [política de segurança](tutorial-security-policy.md). Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-type-of-alerts-are-available"></a>Que tipo de alerta está disponível?
A Central de Segurança do Azure usa uma variedade de [recursos de detecção](security-center-detection-capabilities.md) para alertar os clientes de possíveis ataques direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. As informações incluídas em um alerta variam de acordo com o tipo de análise usado para detectar a ameaça. Os Incidentes também podem conter informações contextuais adicionais que podem ser úteis ao investigar uma ameaça.  Este artigo fornece informações sobre os seguintes tipos de alerta:

* Análise de Comportamento da Máquina Virtual (VMBA)
* Análise de Rede
* Análise do Banco de Dados SQL e SQL Data Warehouse
* Informações Contextuais

## <a name="virtual-machine-behavioral-analysis"></a>Análise de comportamento da máquina virtual
A Central de Segurança do Azure pode usar a análise de comportamento para identificar os recursos comprometidos com base na análise dos logs de evento da máquina virtual. Por exemplo, Eventos de Criação do Processos e Eventos de Logon. Além disso, há uma correlação com outros sinais para verificar se há suporte a evidências de uma campanha generalizada.

> [!NOTE]
> Para saber mais sobre como funcionam os recursos de detecção da Central de Segurança, confira [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md).


### <a name="event-analysis"></a>Análise de eventos
A Central de Segurança usa análises avançadas para identificar recursos comprometidos com base na análise dos logs de evento da máquina virtual. Por exemplo, Eventos de Criação do Processos e Eventos de Logon. Além disso, há uma correlação com outros sinais para verificar se há suporte a evidências de uma campanha generalizada.

* **Execução de processo suspeita detectada**: invasores geralmente tentam executar códigos mal-intencionados sem detecção mascarando-os como processos benignos. Esses alertas indicam que uma execução de processo corresponde a um dos seguintes padrões:
    * Um processo conhecido por ser usado para fins mal-intencionados foi executado. Embora comandos individuais possam parecer benignos, o alerta é classificado com base em uma agregação desses comandos.
    * Um processo foi executado em um local incomum.
    * Um processo foi executado em um local em comum com arquivos suspeitos conhecidos.
    * Um processo foi executado em um caminho suspeito.
    * Um processo foi executado em um contexto anormal.
    * Um processo foi executado por uma conta incomum.
    * Um processo com uma extensão suspeita foi executado.
    * Um processo com uma extensão dupla suspeita foi executado.
    * Um processo com um caractere suspeito da direita para esquerda (RLO) em seu nome de arquivo foi executado.
    * Um processo cujo nome é semelhante, mas é diferente de um processo de execução muito comum foi executado
    * Um processo cujo nome corresponde a uma ferramenta de invasão conhecida foi executado.
    * Um processo com um nome aleatório foi executado.
    * Um processo com uma extensão suspeita foi executado.
    * Um arquivo oculto foi executado.
    * Um processo foi executado como um filho de um outro processo não relacionado.
    * Um processo incomum foi criado por um processo de sistema.
    * Um processo anormal foi iniciado pelo serviço de atualização do Windows.
    * Um processo foi executado com uma linha de comando incomum. Isso foi associado com processos legítimos sendo sequestrados para a execução de conteúdo mal-intencionado.
    * Uma tentativa de iniciar todos os executáveis (*.exe) em um diretório foi executada na linha de comando.
    * Um processo foi executado pelo utilitário PsExec, o qual pode ser usado para executar processos remotamente.
    * O pai executável Apache Tomcat® (Tomcat#.exe) foi usado para iniciar processos filho suspeitos, os quais podem hospedar ou iniciar comandos mal-intencionados.
    * O “Auxiliar de Compatibilidade de Programas” (pcalua.exe) do Microsoft Windows foi usado para iniciar o código executável, o qual poderia ser mal-intencionado.
    * Uma intermitência de encerramento de processo suspeita foi detectada.
    * O processo de sistema SVCHOST foi executado em um contexto anormal.
    * O processo de sistema SVCHOST foi executado em um raro grupo de serviço.
    * Uma linha de comando suspeita foi executada.
    * Um script do PowerShell tem características em comum com scripts suspeitos conhecidos.
    * Um cmdlet Powersploit do PowerShell mal-intencionado e conhecido foi executado.
    * Um usuário interno do SQL executou um processo que normalmente não executaria.
    * Um executável codificado em base 64 foi detectado, o que pode indicar um invasor tentando evitar sua detecção por meio da criação de um executável na hora por meio de uma sequência de comandos.

* **Atividade suspeita de recurso RDP**: os invasores costumam atacar portas de gerenciamento abertas como RDP com ataques de força bruta. Esses alertas indicam atividade de logon suspeita em Área de Trabalho Remota apontando que:
    * Houve tentativas de logon em Área de Trabalho Remota.
    * Houve tentativas de logon em Área de Trabalho Remota usando contas inválidas.
    * Houve tentativas de logon em Área de Trabalho Remota e algumas conseguiram fazer logon no computador.
* **Atividade suspeita de recurso SSH**: os invasores costumam atacar portas de gerenciamento abertas como SSH com ataques de força bruta. Esses alertas indicam atividade de logon suspeita em SSH apontando que:
    * Houve tentativas de logon com falha no SSH.
    * Houve tentativas de logon no SSH, e algumas tiveram êxito.
* **Valor de Registro suspeito do WindowPosition**: esse alerta indica que houve uma tentativa de alteração de configuração do Registro do WindowPosition, o que pode ser um indicativo de ocultação de janelas de aplicativo em seções não visíveis da área de trabalho.
* **Possível tentativa de ignorar o AppLocker**: o AppLocker pode ser usado para limitar os processos que podem ser executados em Windows, limitando a exposição a malware. Esse alerta indica uma possível tentativa de ignorar restrições do AppLocker usando executáveis confiáveis (permitidos pela política do AppLocker) para executar um código não confiável.
* **Comunicações de pipe nomeado suspeitas**: esse alerta indica que os dados foram gravados em um pipe nomeado local do comando de console do Windows. Pipes nomeados são conhecidos por serem usados por invasores para criar tarefas e se comunicar com um implante mal-intencionado.
* **Decodificação de um executável usando a ferramenta interna certutil.exe**: esse alerta indica que um utilitário de conta de administrador interno, certutil.exe, foi usado para decodificar um executável. Os invasores são conhecidos por abusar da funcionalidade de ferramentas de administrador legítimas para executar ações mal-intencionadas, por exemplo, usando uma ferramenta como o certutil.exe para decodificar um executável mal-intencionado que será executado posteriormente.
* **Um log de eventos foi limpo**: esse alerta aponta uma operação de limpeza do log de eventos, normalmente usada por invasores para tentar esconder seus rastros.
* **Desabilitando e excluindo arquivos de log do IIS**: esse alerta indica que o arquivo de log do IIS foi desabilitado e/ou excluído, o que normalmente é usado pelos invasores para tentar esconder seus rastros.
* **Exclusão de arquivo suspeito**: esse alerta indica a exclusão suspeita de arquivos, o que pode ser usado por um invasor para remover evidências de binários mal-intencionados.
* **Todas as cópias de sombra de arquivos foram excluídas**: esse alerta indica que cópias de sombra foram excluídas.
* **Comandos de limpeza de arquivo suspeito**: esse alerta indica uma combinação de comandos systeminfo usada para executar uma atividade de autolimpeza pós-comprometimento.  Embora *systeminfo.exe* seja uma ferramenta legítima do Windows, executá-la duas vezes consecutivas seguida por um comando de exclusão da maneira que ocorreu aqui é raro.
* **Criação de conta suspeita**: esse alerta indica que foi criada uma conta bastante semelhante a uma conta interna com privilégio administrativo. Essa técnica pode ser usada para criar uma conta de invasor sem ser detectada.
* **Atividade de cópia de sombra de volume suspeita**: esse alerta indica a atividade de exclusão de cópia de sombra no recurso. Cópia de sombra de volume (VSC) é um artefato importante que armazena instantâneos de dados. Essa atividade é associada a Ransomware, mas também poderia ser legítima.
* **Método de persistência de Registro do Windows**: esse alerta indica uma tentativa de manter um executável no Registro do Windows. Um malware geralmente usa essa técnica para sobreviver a uma inicialização.
* **Nova regra de firewall suspeita**: esse alerta indica que uma nova regra de firewall foi adicionada por meio do *netsh.exe* para permitir o tráfego de um executável em um local suspeito.
* **Execuções de XCOPY suspeitas**: esse alerta indica uma série de execuções de XCOPY, o que pode sinalizar que um dos seus computadores foi comprometido e usado para propagar o malware.
* **Supressão de aviso legal exibido aos usuários no logon**: esse alerta indica uma alteração na chave do Registro que controla se um aviso legal é exibido aos usuários quando eles entram. Essa é uma atividade comum realizada por invasores depois de terem comprometido um host.
* **Detectada mistura anormal de caracteres maiúsculos e minúsculos na linha de comando**: esse alerta indica o uso de uma combinação de caracteres maiúsculos e minúsculos na linha de comando, que é uma técnica usada pelos invasores para ocultar da regra de computador baseada em hash ou diferenciação de maiúsculas e minúsculas.
* **Linha de comando ofuscada**: esse alerta indica que os indicadores suspeitos de ofuscação foram detectados na linha de comando.
* **Várias contas de domínio consultadas**: invasores geralmente fazem consultas a contas de domínio do AD enquanto fazem o reconhecimento de usuários, contas de administrador de domínio, controladores de domínio e relações de confiança entre domínios. Esse alerta indica que um número incomum de contas de domínio diferentes foi consultado dentro de um curto período de tempo.
* **Possível atividade de reconhecimento de local**: esse alerta indica que foi executada uma combinação de comandos systeminfo associada à atividade de reconhecimento.  Embora *systeminfo.exe* seja uma ferramenta legítima do Windows, é raro que ela seja executada duas vezes consecutivas.
* **Possível execução de executável keygen**: esse alerta indica que um processo cujo nome é uma indicação de uma ferramenta keygen foi executado. Essas ferramentas são normalmente usadas para anular os mecanismos de licenciamento de software, mas seu download geralmente vem em um pacote com outros softwares mal-intencionados.
* **Execução suspeita via rundll32.exe**: esse alerta indica que rundll32.exe foi usado para executar um processo com um nome incomum, consistente com o esquema de nomenclatura de processo usado por invasores para instalar um primeiro estágio de implantação em um host comprometido.
* **Combinação suspeita de HTA e PowerShell**: esse alerta indica que um HTA (Host de Aplicativo HTML) da Microsoft está iniciando comandos do PowerShell. Essa é uma técnica usada pelos invasores para iniciar scripts do PowerShell mal-intencionados.
* **Alteração para uma chave do Registro que pode ser usada para ignorar o UAC**: esse alerta indica que uma chave do Registro que pode ser usada para ignorar o UAC (controle de conta de usuário) foi alterada, o que geralmente é usado por invasores para se mover de um usuário sem privilégios (usuário padrão) para um acesso privilegiado (por exemplo, administrador) em um host comprometido.
* **Uso de nome de domínio suspeito na linha de comando**: esse alerta indica que um nome de domínio suspeito foi usado, o que pode ser evidência de que um invasor está hospedando ferramentas mal-intencionados e como pontos de extremidade para o comando e controle e pesquisa de dados.
* **Uma conta foi criada em vários hosts em um período de 24 horas**: esse alerta indica que foi feita uma tentativa para criar a mesma conta de usuário em vários hosts, o que pode ser evidência de um invasor se movendo lateralmente na rede depois de uma ou mais entidades de rede terem sido comprometidas.
* **Uso suspeito de CACLS para diminuir o estado de segurança do sistema**: esse alerta indica que a CACLS (lista de controle de acesso de alteração) foi alterada. Essa técnica geralmente é usada pelos invasores para fornecer acesso completo a binários do sistema como ftp.exe, net.exe, wscript.exe, etc.
* **Parâmetros de ataque de Tíquete Dourado do Kerberos**: esse alerta indica que parâmetros de linha de comando consistentes com um ataque de tíquete de ouro Kerberos foram executados. Uma chave krbtgt comprometida pode ser usada por um invasor para representar qualquer usuário desejado.
* **Habilitação da chave do Registro WDigest UseLogonCredential**: esse alerta indica que a chave do Registro foi alterada para permitir que credenciais de entrada sejam armazenadas em texto não criptografado na memória LSA, que pode ser coletado da memória.
* **Uso potencialmente suspeito de ferramenta Telegram**: esse alerta indica que o Telegram foi instalado, um serviço de mensagens instantâneas grátis baseado em nuvem usado por invasores para transferir binários mal-intencionados para qualquer outro computador, telefone ou tablet.
* **Criação de um novo ASEP**: esse alerta indica a criação de um novo ASEP (ponto de extensibilidade de início automático), que faz com que o nome do processo identificado na linha de comando seja iniciado automaticamente e possa ser usado por um invasor para obter persistência.
* **Alterações suspeitas de Set-ExecutionPolicy e WinRM**: esse alerta indica alterações de configuração associadas ao uso de webshell ChinaChopper mal-intencionado.
* **Desabilitação de serviços críticos**: esse alerta indica que o comando “net.exe stop” foi usado para interromper os serviços críticos como SharedAccess ou Central de Segurança do Windows.
* **Uso suspeito de opção de FTP -s**: esse alerta indica o uso de opção de FTPs "-s", que pode ser usado por um malware para se conectar a um servidor FTP e baixar binários mal-intencionados adicionais.
* **Execução suspeita do comando VBScript.Encode**: esse alerta indica que o comando *VBScript.Encode* foi executado, o que codifica scripts em texto ilegível, tornando mais difícil o exame do código pelos usuários.
* **Alocação de objeto VBScript HTTP**: esse alerta indica a criação de um arquivo VBScript usando o Prompt de comando, o que pode ser usado para baixar arquivos mal-intencionados.
* **Ataque a teclas sticky**: esse alerta indica que um invasor pode estar subvertendo um binário de acessibilidade (por exemplo teclas sticky, teclado na tela, narrador) para fornecer acesso a backdoor.
* **Indicadores de ransomware Petya**: esse alerta indica que as técnicas associadas ao ransomware Petya foram observadas.
* **Tentativa de desabilitar a AMSI**: esse alerta indica uma tentativa de desabilitar a interface de verificação antimalware (AMSI), o que pode desabilitar a detecção antimalware.
* **Indicadores de ransomware**: esse alerta indica atividades suspeitas tradicionalmente associadas a ransomware de tela de bloqueio e de criptografia.
* **Arquivo de saída de coleta de rastreamento suspeito**: esse alerta indica que um rastreamento (por exemplo, da atividade de rede) foi coletado e gerou um tipo de arquivo incomum.
* **Software de alto risco**: esse alerta indica o uso de software que foi associado com à instalação de malware. Os invasores geralmente empacotam um malware com ferramentas benignas, como a mostrada nesse, e instalam o malware silenciosamente em segundo plano.
* **Criação de arquivo suspeito**: esse alerta indica a criação ou a execução de um processo usado pelos invasores para baixar malware adicional para um host comprometido depois de um anexo em um documento de phishing ter sido aberto.
* **Credenciais suspeitas na linha de comando**: esse alerta indica que uma senha suspeita foi usada para executar um arquivo. Essa técnica tem sido usada por invasores para executar o malware Pirpi.
* **Possível execução de dropper de malware**: esse alerta indica um nome de arquivo usado por invasores para instalar malware.
* **Execução suspeita via rundll32.exe**: esse alerta indica que rundll32.exe está sendo usado para executar um notepad.exe ou reg.exe, consistente com a técnica de injeção de processo usada por invasores.
* **Argumentos de linha de comando suspeitos**: esse alerta indica argumentos de linha de comando suspeitos que têm sido usados em conjunto com um shell inverso usado pelo grupo de atividade HYDROGEN.
* **Credenciais de documento suspeitas**: esse alerta indica que um hash de senha comum e pré-calculado suspeito e usado por malware está sendo usado para executar um arquivo.
* **Criação de script do PS dinâmico**: esse alerta indica que um script do PowerShell que está sendo criado dinamicamente. Os invasores usam essa técnica para compilar um script progressivamente para fugir de sistemas IDS.
* **Indicadores de Metasploit**: esse alerta indica atividades associadas com a estrutura Metasploit, que fornece uma variedade de ferramentas e recursos ao invasor.
* **Atividade suspeita na conta**: esse alerta indica uma tentativa de se conectar a um computador usando uma conta que foi comprometida recentemente.
* **Criação de conta**: esse alerta indica a criação de uma nova conta no computador.


### <a name="crash-analysis"></a>Análise de falha


A análise do despejo de memória é um método usado para detectar um malware sofisticado capaz de escapar das soluções de segurança tradicionais. Várias formas de malware tentam reduzir a chance de serem detectadas por produtos antivírus nunca gravando no disco ou criptografando os componentes de software gravados no disco. Esta técnica torna o malware difícil de detectar usando as abordagens tradicionais antimalware. No entanto, esse tipo de malware pode ser detectado usando a análise de memória, pois o malware deve deixar rastreamentos na memória para poder funcionar.

Quando o software falha, um despejo de memória captura uma parte da memória no momento da falha. A falha pode ser causada por malware, problemas gerais do aplicativo ou do sistema. Analisando a memória no despejo de falha, a Central de Segurança pode detectar as técnicas usadas para explorar as vulnerabilidades no software, acessar os dados confidenciais e manter-se de maneira furtiva em uma máquina comprometida. Isso é feito com um mínimo de impacto no desempenho para os hosts quando a análise é executada pelo back-end da Central de Segurança.

* **Injeção de código descoberta**: A injeção de código é a inserção de módulos executáveis em processos ou threads em execução. Essa técnica é usada por malware para acessar dados, ocultar ou impedir sua remoção (por exemplo, persistência). Esse alerta indica que um módulo injetado está presente no despejo de memória. Os desenvolvedores de software legítimo ocasionalmente executam a injeção de código por motivos não mal-intencionados, como modificar ou estender um aplicativo existente ou um componente do sistema operacional. Para ajudar a diferenciar módulos injetados mal-intencionados dos não mal-intencionados, a Central de Segurança verifica se o módulo injetado se encaixa em um perfil comportamento suspeito. O resultado dessa verificação é indicado pelo campo "ASSINATURA" do alerta e refletido na gravidade, na descrição e nas etapas de solução do alerta.
* **Segmento de código suspeito**: o alerta de segmento de código suspeito indica que um segmento de código foi alocado usando métodos não padrão, como os usados pela injeção refletiva e pelo esvaziamento de processo. Características adicionais do segmento de código são processadas para fornecer contexto aos recursos e comportamentos do segmento de código relatado.
* **Shellcode descoberto**: Shellcode é a carga executada depois que o malware explorou uma vulnerabilidade do software. Esse alerta indica que a análise do despejo de memória detectou um código executável com um comportamento normalmente executado por cargas mal-intencionadas. Embora software que não seja mal-intencionado possa executar esse comportamento, não é comum às práticas de desenvolvimento de software normal.
* **Sequestro do módulo descoberto**: O Windows usa DLLs (bibliotecas de vínculo dinâmico) para permitir que o software use funcionalidades comuns de sistema do Windows. O sequestro de DLL ocorre quando o malware altera a ordem de carregamento de DLL para carregar cargas maliciosas na memória, onde o código arbitrário pode ser executado. Este alerta indica que a análise de despejo de memória detectou um módulo com um nome parecido carregado de dois caminhos diferentes. Um dos caminhos carregados vem de um local de binários de sistema comuns do Windows. Os desenvolvedores de software legítimos ocasionalmente alteram a ordem de carregamento de DLL por motivos lícitos, por exemplo, para instrumentação, extensão do sistema operacional Windows ou de aplicativos do Windows. Para ajudar a diferenciar alterações mal-intencionadas das bem-intencionadas na ordem de carregamento de DLL, a Central de Segurança verifica se um módulo carregado se encaixa em um perfil suspeito.
* **Módulo do Windows simulado detectado**: O malware pode usar nomes comuns de binários (por exemplo, SVCHOST.EXE) ou módulos (por exemplo, NTDLL. DLL) do sistema Windows para se misturar e ocultar a natureza do software mal-intencionado dos administradores de sistema. O alerta indica que o arquivo de despejo de memória contém módulos que usam nomes de módulo do sistema Windows, mas não atendem a outros critérios típicos de módulos do Windows. A análise da cópia em disco do módulo simulado pode fornecer mais informações sobre a natureza legítima ou mal-intencionada do módulo.
* **Binário do sistema modificado descoberto**: Malware pode modificar os principais binários do sistema para acessar dados secretamente ou persistir furtivamente em um sistema comprometido. O alerta indica que a análise de despejo de memória detectou binários principais do sistema operacional Windows que foram modificados na memória ou no disco. Os desenvolvedores de software legítimos ocasionalmente modificam módulos do sistema na memória por motivos lícitos, por exemplo, para desvios ou para compatibilidade de aplicativos. Para ajudar a diferenciar módulos bem-intencionadas de mal-intencionados, a Central de Segurança verifica se um módulo alterado se encaixa em um perfil suspeito.

## <a name="network-analysis"></a>Análise de Rede
A detecção de ameaças da rede da Central de Segurança funciona coletando automaticamente as informações de segurança de seu tráfego do Azure IPFIX (Internet Protocol Flow Information Export). Ele analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças.

* **Possíveis tentativas de força bruta SQL de entrada**: a análise de tráfego de rede detectou comunicação suspeita de entrada SQL. Esta atividade é consistente com as tentativas de força bruta contra servidores SQL.
* **Atividade suspeita de rede RDP de entrada de várias fontes**: a análise de tráfego de rede detectou comunicação de protocolo RDP de entrada anormal de várias fontes. Especificamente, dados de amostra de rede mostram IPs exclusivos conectando-se ao seu computador, o que é considerado anormal para esse ambiente. Essa atividade pode indicar uma tentativa de força bruta no ponto de extremidade RDP em vários hosts (Botnet).
* **Atividade suspeita de rede RDP de entrada**: a análise de tráfego de rede detectou comunicação de protocolo RDP de entrada anormal. Especificamente, dados de amostra de rede mostram um alto número de conexões de entrada ao seu computador, o que é considerado anormal para esse ambiente. Essa atividade pode indicar uma tentativa de força bruta no ponto de extremidade RDP.
* **Atividade suspeita de rede RDP de saída para vários destinos**: a análise de tráfego de rede detectou comunicação de protocolo RDP de saída anormal para vários destinos. Essa atividade pode indicar que o computador foi comprometido e agora está sendo usado força bruta em pontos de extremidade RDP externos. Observe que esse tipo de atividade poderia possivelmente fazer com que seu IP fosse sinalizado como mal-intencionado por entidades externas.
* **Suspeita de atividade de rede RDP de saída**: a análise de tráfego de rede detectou comunicação de protocolo RDP de saída anormal. Especificamente, dados de amostra de rede mostram um alto número de conexões de saída do seu computador, o que é considerado anormal para esse ambiente. Essa atividade pode indicar que o computador foi comprometido e agora está sendo usado força bruta em pontos de extremidade RDP externos. Observe que esse tipo de atividade poderia possivelmente fazer com que seu IP fosse sinalizado como mal-intencionado por entidades externas.
* **Atividade suspeita de rede SSH de entrada**: Análise de tráfego de rede detectou comunicação SSH de entrada anormal. Especificamente, dados de amostra de rede mostram um alto número de conexões de entrada ao seu computador, o que é considerado anormal para esse ambiente. Essa atividade pode indicar uma tentativa de força bruta no ponto de extremidade do SSH.
* **Atividade suspeita de rede SSH de entrada de várias fontes**: Análise de tráfego de rede detectou comunicação SSH de entrada anormal. Especificamente, dados de amostra de rede mostram IPs exclusivos conectando-se ao seu computador, o que é considerado anormal para esse ambiente. Essa atividade pode indicar uma tentativa de força bruta no ponto de extremidade do SSH em vários hosts (Botnet).
* **Suspeita de atividade de rede SSH de saída**: a análise de tráfego de rede detectou comunicação SSH de saída anormal. Especificamente, dados de amostra de rede mostram um alto número de conexões de saída do seu computador, o que é considerado anormal para esse ambiente. Essa atividade pode indicar que o computador foi comprometido e agora está sendo usado força bruta em pontos de extremidade externos do SSH. Observe que esse tipo de atividade poderia possivelmente fazer com que seu IP fosse sinalizado como mal-intencionado por entidades externas.
* **Atividade suspeita de rede SSH de saída para vários destinos**: a análise de tráfego de rede detectou comunicação SSH de saída anormal para vários destinos. Especificamente, dados de amostra de rede mostram seu computador conectando-se a IP exclusivos, o que é considerado anormal para esse ambiente. Essa atividade pode indicar que o computador foi comprometido e agora está sendo usado força bruta em pontos de extremidade externos do SSH. Observe que esse tipo de atividade poderia possivelmente fazer com que seu IP fosse sinalizado como mal-intencionado por entidades externas.
* **Comunicação de rede com um computador mal-intencionado detectada**: a análise de tráfego de rede indica que seu computador se comunicou com o que possivelmente é um centro de Comando e Controle.
* **Detectado computador possivelmente comprometido**: a análise de tráfego de rede detectou atividade de saída, o que pode indicar que ele está atuando como parte de um botnet. A análise é baseada em IPs acessados pelo seu recurso junto com os registros DNS públicos.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>Análise do Banco de Dados SQL e SQL Data Warehouse

A análise de recursos da Central de Segurança concentra-se em serviços plataforma como serviço (PaaS), como a integração com a [Detecção de ameaças do Banco de Dados SQL do Azure](../sql-database/sql-database-threat-detection.md) e o SQL Data Warehouse do Azure. A Detecção de Ameaças do SQL detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e dispara os seguintes alertas:

* **Vulnerabilidade a injeção de SQL**: esse alerta é disparado quando um aplicativo gera uma instrução SQL com erro no banco de dados. Isso pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Há dois motivos possíveis para a geração de uma instrução com erro:
    * Um defeito no código do aplicativo que cria a instrução SQL com erro
    * O código do aplicativo ou procedimentos armazenados não limpam a entrada do usuário ao construir a instrução SQL com erro, o que pode ser explorado para injeção de SQL
* **Potencial injeção de SQL**: esse alerta é disparado quando acontece uma exploração ativa contra uma vulnerabilidade identificada do aplicativo para injeção de SQL. Isso significa que o invasor está tentando inserir instruções SQL maliciosas usando o código de aplicativo ou procedimentos armazenados vulneráveis.
* **Acesso de um local incomum**: este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém faz logon no SQL Server de um local geográfico incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
* **Acesso de um data center do Azure incomum**: esse alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém faz logon no SQL Server de um data center do Azure incomum que foi visto neste servidor durante um período recente. Em alguns casos, o alerta detecta uma ação legítima (seu novo aplicativo no Azure, o Power BI, Azure SQL Query Editor). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
* **Acesso de uma entidade de segurança desconhecida**: esse alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém faz logon no SQL Server de uma entidade de segurança incomum (usuário SQL). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
* **Acesso de um aplicativo potencialmente prejudicial**: esse alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
* **Credenciais SQL de força bruta**: esse alerta é disparado quando há um número alto e anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="contextual-information"></a>Informações Contextuais
Durante uma investigação, os analistas precisarão de contexto extra para atingirem um veredito sobre a natureza da ameaça e como resolvê-la.  Por exemplo, uma anomalia de rede foi detectada, mas sem compreender o que está acontecendo na rede ou em relação ao recurso de destino é cada difíceis de entender as ações a serem tomadas. Para ajudar com isso, um incidente de segurança pode incluir artefatos, eventos relacionados e informações que podem ajudar quem estiver investigando. A disponibilidade de informações adicionais irá variar com base no tipo de ameaça detectada e an configuração do seu ambiente e não estará disponível para todos os incidentes de segurança.

Se houver informações adicionais disponíveis, elas serão mostradas no Incidente de Segurança abaixo da lista de alertas. Isso pode conter informações como:

- Limpar eventos do log
- Dispositivo PNP conectado de dispositivo desconhecido
- Alertas que não são acionáveis
- Nova criação de conta
- Arquivo decodificado usando a ferramenta certutil

![Alerta de acesso incomum](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre os diferentes tipos de alertas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Manipulação de incidente de segurança na Central de Segurança do Azure](security-center-incident.md)
* [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre postagens no blog sobre a conformidade e segurança do Azure.

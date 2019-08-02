---
title: Dimensionar automaticamente os hosts de sessão de visualização de área de trabalho Virtual do Windows - Azure
description: Descreve como configurar o script de colocação em escala automática para hosts de sessão de visualização de área de trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 3b98db361a8ec888eb8bf9e1bf3658a7e38111c6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620413"
---
# <a name="automatically-scale-session-hosts"></a>Dimensionar automaticamente hosts da sessão

Para muitas implantações de visualização de área de trabalho Virtual do Windows no Azure, os custos de máquina virtual representam uma parte significativa do custo total de implantação de área de trabalho Virtual do Windows. Para reduzir os custos, é melhor desligar e desalocar a sessão hospedar máquinas virtuais (VMs) durante o horário de pico, reiniciá-los durante horários de pico.

Este artigo usa um script simples de dimensionamento para dimensionar automaticamente as máquinas virtuais de host de sessão em seu ambiente de área de trabalho Virtual do Windows. Para saber mais sobre como funciona o script de colocação em escala, consulte a [como funciona o script de colocação em escala](#how-the-scaling-script-works) seção.

## <a name="prerequisites"></a>Pré-requisitos

O ambiente em que você executar o script deve ter o seguinte:

- Um locatário de área de trabalho Virtual do Windows e a conta ou uma entidade de serviço com permissões para consultar esse locatário (por exemplo, o colaborador de RDS).
- VMs do pool host de sessão configurado e registrado com o serviço de área de trabalho Virtual do Windows.
- Uma máquina virtual adicional que executa a tarefa agendada por meio do Agendador de tarefas e tem acesso à rede para hosts de sessão. Isso será ser chamado posteriormente neste documento de VM do scaler.
- O [módulo Microsoft Azure Resource Manager PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instalado na VM que executa a tarefa agendada.
- O [módulo Windows PowerShell de área de trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) instalado na VM que executa a tarefa agendada.

## <a name="recommendations-and-limitations"></a>Limitações e recomendações

Ao executar o script de colocação em escala, manter o seguinte em mente:

- Esse script de colocação em escala pode manipular apenas um pool de host por instância da tarefa agendada que está executando o script de colocação em escala.
- As tarefas agendadas que executam scripts de colocação em escala devem estar em uma VM que está sempre ativada.
- Crie uma pasta separada para cada instância do script colocação em escala e sua configuração.
- Esse script não dá suporte a fazer logon como um administrador para a área de trabalho Virtual do Windows com contas de usuário do AD do Azure que exigem a autenticação multifator. É recomendável que usar entidades de serviço para acessar o serviço de área de trabalho Virtual do Windows e o Azure. Siga [este tutorial](create-service-principal-role-powershell.md) para criar uma entidade de serviço e uma atribuição de função com o PowerShell.
- Garantia de SLA do Azure se aplica apenas às VMs no conjunto de disponibilidade. A versão atual do documento descreve um ambiente com uma única VM fazendo o dimensionamento, que pode não atender aos requisitos de disponibilidade.

## <a name="deploy-the-scaling-script"></a>Implantar o script de colocação em escala

Os procedimentos a seguir informará a você como implantar o script de colocação em escala.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Preparar o ambiente para o script de colocação em escala

Primeiro, prepare seu ambiente para o script de colocação em escala:

1. Entrar para a máquina virtual (VM scaler) que executará a tarefa agendada com uma conta de administrador de domínio.
2. Crie uma pasta na VM para manter o script de colocação em escala e sua configuração e a escala (por exemplo, **c:\\HostPool1 dimensionamento**).
3. Baixe o **basicScale.ps1**, **config. XML**, e **funções PSStoredCredentials.ps1** arquivos e o **PowershellModules** pasta do [dimensionando o repositório de scripts](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) e copiá-los para a pasta que você criou na etapa 2. Há duas maneiras principais de obter os arquivos antes de copiá-los para a VM de scaler:
    - Clone o repositório git em seu computador local.
    - Modo de exibição de **Raw** versão de cada arquivo, copie e cole o conteúdo de cada arquivo para um editor de texto e salve os arquivos com o nome de arquivo correspondente e o tipo de arquivo. 

### <a name="create-securely-stored-credentials"></a>Criar credenciais armazenadas com segurança

Em seguida, você precisará criar as credenciais armazenadas com segurança:

1. Abra o ISE do PowerShell como administrador.
2. Importe o módulo do PowerShell de RDS executando o seguinte cmdlet:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Abra o painel de edição e carregue o **PSStoredCredentials.ps1 função** arquivo.
4. Execute o cmdlet a seguir:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Por exemplo, **Set-Variable - nome KeyPath-escopo Global-valor "c:\\HostPool1 dimensionamento"**
5. Execute o **StoredCredential New - KeyPath \$KeyPath** cmdlet. Quando solicitado, insira suas credenciais de área de trabalho Virtual do Windows com permissões para consultar o pool de host (o pool de host é especificado na **config. XML**).
    - Se você usar entidades de serviço diferentes ou a conta padrão, execute as **StoredCredential New - KeyPath \$KeyPath** cmdlet depois de cada conta para criar um local as credenciais armazenadas.
6. Execute **StoredCredential Get-lista** para confirmar as credenciais foram criadas com êxito.

### <a name="configure-the-configxml-file"></a>Configurar o arquivo config. XML

Insira os valores relevantes para os campos a seguir para atualizar as configurações de script de colocação em escala em config. XML:

| Campo                     | DESCRIÇÃO                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | ID de locatário do AD do Azure que associa a assinatura em que executa o host de sessão de VMs     |
| AADApplicationId              | ID da entidade de segurança de aplicativo de serviço                                                       |
| AADServicePrincipalSecret     | Isso pode ser inserido durante a fase de teste, mas deve ser mantido vazio, depois de criar as credenciais com **PSStoredCredentials.ps1 de funções**    |
| currentAzureSubscriptionId    | A ID da assinatura do Azure em que executa o host de sessão de VMs                        |
| tenantName                    | Nome do locatário de área de trabalho Virtual do Windows                                                    |
| hostPoolName                  | Nome do pool de host de área de trabalho Virtual do Windows                                                 |
| RDBroker                      | Padrão de URL para o serviço WVD, valor https:\//rdbroker.wvd.microsoft.com             |
| Nome de usuário                      | A ID de entidade de segurança de aplicativo de serviço (é possível ter a mesma entidade de serviço, conforme mostrado no AADApplicationId) ou o usuário padrão sem a autenticação multifator |
| isServicePrincipal            | Os valores aceitos são **verdadeira** ou **falso**. Indica se o segundo conjunto de credenciais que está sendo usado é uma entidade de serviço ou uma conta padrão. |
| BeginPeakTime                 | Quando o tempo de uso de pico começa                                                            |
| EndPeakTime                   | Quando termina o tempo de uso de pico                                                              |
| TimeDifferenceInHours         | Diferença de tempo entre a hora local e o UTC, em horas                                   |
| SessionThresholdPerCPU        | Número máximo de sessões por limite de CPU usado para determinar quando um novo host da sessão VM precisa ser iniciada durante o horário de pico.  |
| MinimumNumberOfRDSH           | Número mínimo de VMs continue em execução durante o horário de pico de uso do pool de host             |
| LimitSecondsToForceLogOffUser | Número de segundos a aguardar antes de forçar os usuários a sair. Se definido como 0, os usuários não sejam forçado a sair.  |
| LogOffMessageTitle            | Título da mensagem enviada a um usuário antes de eles são forçados a fazer logoff                  |
| LogOffMessageBody             | Corpo da mensagem de aviso enviada aos usuários antes que eles são desconectados. Por exemplo, "Este computador será desligado para baixo na X minutos. Salve seu trabalho e saia." |

### <a name="configure-the-task-scheduler"></a>Configurar o Agendador de tarefas

Depois de configurar o arquivo de configuração. XML, você precisará configurar o Agendador de tarefas para executar o arquivo de basicScaler.ps1 em intervalos regulares.

1. Inicie **Agendador de tarefas**.
2. No **Agendador de tarefas** janela, selecione **criar tarefa...**
3. Na **criar tarefa** caixa de diálogo, selecione o **gerais** , insira um **nome** (por exemplo, "RDSH dinâmico"), selecione **Executar estando o usuário conectado ou não** e **executar com privilégios mais altos**.
4. Vá para o **disparadores** guia e, em seguida, selecione **New...**
5. No **novo gatilho** caixa de diálogo, em **configurações avançadas**, marque **repetir a tarefa cada** e selecione o período apropriado e a duração (por exemplo, **15 minutos** ou **indefinidamente**).
6. Selecione o **ações** guia e **New...**
7. No **nova ação** caixa de diálogo, insira **powershell.exe** para o **programa/script** campo e, em seguida, insira **c:\\dimensionamento\\ RDSScaler.ps1** para o **adicionar argumentos (opcional)** campo.
8. Vá para o **condições** e **configurações** guias e selecione **Okey** para aceitar as configurações padrão para cada um.
9. Insira a senha da conta administrativa, em que você planeja executar o script de colocação em escala.

## <a name="how-the-scaling-script-works"></a>Como funciona o script de colocação em escala

Esse script de colocação em escala lê os parâmetros de um arquivo config. XML, incluindo o início e término do período de uso de pico durante o dia.

Durante o tempo de uso de pico, o script verifica o número atual de sessões e a capacidade RDSH em execução atual para cada pool de host. Ele calcula se o host da sessão VMs em execução tem capacidade suficiente para dar suporte a sessões existentes com base no parâmetro SessionThresholdPerCPU definido no arquivo config. XML. Caso contrário, o script inicia o host de sessão adicional VMs no pool de host.

Durante o tempo de uso fora de pico, o script determina qual host de sessão de VMs devem ser desligado com base no parâmetro MinimumNumberOfRDSH no arquivo config. XML. O script definirá a sessão de VMs de host para esvaziar o modo para impedir que novas sessões, conectando aos hosts. Se você definir a **LimitSecondsToForceLogOffUser** parâmetro no arquivo config. XML para um inteiro positivo diferente de zero, o script irá notificar qualquer atualmente conectado aos usuários salvar trabalho, aguardar o período de tempo configurado e, em seguida, forçar o usuários a sair. Depois que todas as sessões de usuário foram aprovadas em uma VM do host de sessão, o script irá desligar o servidor.

Se você definir a **LimitSecondsToForceLogOffUser** parâmetro no arquivo config. XML para zero, o script permitirá que a definição de configuração de sessão no host de propriedades do pool lidar com assinatura logoff das sessões do usuário. Se não houver nenhuma sessão em uma VM do host de sessão, ele deixará o host de sessão de VM em execução. Se não existem todas as sessões, o script desligará a VM do host de sessão.

O script foi desenvolvido para ser executado periodicamente no servidor VM de scaler, usando o Agendador de tarefas. Selecione o intervalo de tempo apropriado com base no tamanho do seu ambiente de serviços de área de trabalho remota e lembre-se de que o iniciar e desligar as máquinas virtuais podem levar algum tempo. É recomendável executar o script de colocação em escala a cada 15 minutos.

## <a name="log-files"></a>Arquivos de log

O script de colocação em escala cria dois arquivos de log **WVDTenantScale.log** e **WVDTenantUsage.log**. O **WVDTenantScale.log** arquivo registrará em log os eventos e erros (se houver) durante cada execução do script de colocação em escala.

O **WVDTenantUsage.log** arquivo registrará o Active Directory número de núcleos e número de ativo de máquinas virtuais sempre que você executar o script de colocação em escala. Você pode usar essas informações para estimar o uso real de VMs do Microsoft Azure e o custo. O arquivo é formatado como valores separados por vírgula, com cada item que contém as seguintes informações:

>tempo, o pool de host, núcleos de VMs

O nome do arquivo também pode ser modificado para ter uma extensão. csv, carregadas no Microsoft Excel e analisados.

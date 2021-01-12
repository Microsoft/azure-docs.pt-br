---
title: Usando a licença do Microsoft defender para ponto de extremidade incluída na central de segurança do Azure
description: Saiba mais sobre o Microsoft defender para ponto de extremidade e como implantá-lo na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: memildin
ms.openlocfilehash: 34d8881eb072c4c723bf68b7c6ccfd24ab19cd3c
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116272"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Proteja seus pontos de extremidade com a solução EDR integrada da central de segurança: Microsoft defender para Endpoint

O Microsoft defender for Endpoint é uma solução de segurança de ponto de extremidade holística e fornecida na nuvem. Seus principais recursos são:

- Avaliação e gerenciamento de vulnerabilidades com base em risco 
- Redução da superfície de ataque
- Proteção baseada em comportamento e com capacidade de nuvem
- Detecção e resposta de ponto de extremidade (EDR)
- Investigação e correção automáticas
- Serviços de busca gerenciados

> [!TIP]
> Originalmente iniciado como **Windows Defender ATP**, esse produto de EDR (detecção e resposta de ponto de extremidade) foi renomeado em 2019 como **Microsoft defender ATP**.
>
> No Ignite 2020, lançamos o [Microsoft defender XDR Suite](https://www.microsoft.com/security/business/threat-protection) e esse componente EDR foi renomeado **como Microsoft defender for Endpoint**.


## <a name="availability"></a>Disponibilidade

| Aspecto                          | Detalhes                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado da versão:                  | GA (em disponibilidade geral)                                                                                                                                                                                                                                                                                      |
| Preço:                        | Requer [Azure Defender para Servidores](security-center-pricing.md)                                                                                                                                                                                                                                             |
| Plataformas compatíveis:            | Máquinas do Azure executando o Windows<br>Computadores do Arc do Azure executando o Windows|
| Versões do Windows com suporte:  |  • A central de segurança dá suporte à detecção no Windows Server 2016, 2012 R2 e 2008 R2 SP1<br> • O monitoramento do ponto de extremidade do servidor usando essa integração foi desabilitado para clientes do Office 365 GCC<br> • Não há suporte para Windows Server 2019, Windows 10 1703 (e mais recente) ou Linux|
| Funções e permissões necessárias: | Para habilitar/desabilitar a integração: **administrador de segurança** ou **proprietário**<br>Para exibir alertas do MDATP na central de segurança: **leitor de segurança**, **leitor**, colaborador do **grupo de recursos**, proprietário do **grupo de recursos**, administrador de **segurança**, **proprietário da assinatura** ou colaborador da **assinatura**|
| Nuvens:                         | ![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) Governo da China e outros governos<br>![Não](./media/icons/no-icon.png) Clientes GCC executando cargas de trabalho em nuvens globais do Azure                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |


## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Recursos do Microsoft defender para ponto de extremidade na central de segurança

O Microsoft defender for Endpoint fornece:

- **Sensores avançados de detecção de pós-violação**. Os sensores do defender for Endpoint para computadores Windows coletam uma vasta gama de sinais comportamentais.

- **Detecção de pós-violação, baseada em análises e com base na análise**. O defender for Endpoint se adapta rapidamente às ameaças em constante mudança. Ele usa análise avançada e Big Data. Ele é amplificado pelo poder da Gráfico de Segurança Inteligente com sinais entre o Windows, o Azure e o Office para detectar ameaças desconhecidas. Ele fornece alertas acionáveis e permite que você responda rapidamente.

- **Inteligência contra ameaças**. O defender for Endpoint gera alertas ao identificar ferramentas, técnicas e procedimentos do invasor. Ele usa dados gerados pelos caçadores de ameaças da Microsoft e pelas equipes de segurança, incrementados por inteligência oferecida por parceiros.

Ao integrar o defender for Endpoint à central de segurança, você se beneficiará dos seguintes recursos adicionais:

- **Integração automatizada**. A central de segurança habilita automaticamente o sensor Microsoft defender for Endpoint para todos os servidores Windows monitorados pela central de segurança. Exceto aqueles que executam o Windows Server 2019, que deve ser integrado por meio de script local, objeto de Política de Grupo (GPO) ou [Microsoft Endpoint Configuration Manager](https://docs.microsoft.com/mem/configmgr/) (anteriormente SCCM).

- **Painel único de vidro**. O console da central de segurança exibe o Microsoft defender para alertas de ponto de extremidade. Para investigar ainda mais, use as páginas do portal da própria Microsoft defender para o ponto de extremidade, em que você verá informações adicionais, como a árvore de processo de alerta e o grafo de incidentes. Você também pode ver uma linha do tempo detalhada do computador, mostrando cada comportamento por um período de histórico de até seis meses.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Central de segurança da própria Microsoft defender para ponto de extremidade" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Local do locatário do Microsoft defender para ponto de extremidade

Quando você usa a central de segurança do Azure para monitorar seus servidores, um locatário do Microsoft defender para ponto de extremidade é criado automaticamente. Os dados coletados pelo defender para ponto de extremidade são armazenados na localização geográfica do locatário, conforme identificado durante o provisionamento. Dados do cliente – no formulário pseudônimos – também podem ser armazenados no armazenamento central e nos sistemas de processamento no Estados Unidos. 

Depois de configurar o local, você não poderá alterá-lo. Se você precisar mover seus dados para outro local, entre em contato com Suporte da Microsoft para redefinir o locatário.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Habilitando a integração do Microsoft defender para ponto de extremidade

1. Habilitar o **Azure defender para servidores**. Consulte os [preços da central de segurança do Azure](security-center-pricing.md#enable-azure-defender).

    > [!NOTE]
    > Para proteger suas máquinas habilitadas para o Arc do Azure, use as instruções em [início rápido: conectar máquina híbrida com servidores habilitados para Arc do Azure](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).

1. Se você já tiver licenciado e implantado o Microsoft defender para pontos de extremidade em seus servidores, remova-o usando o procedimento descrito em [transferir Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers).
1. No menu da Central de Segurança, selecione **Preço e configurações**.
1. Selecione a assinatura que deseja alterar.
1. Selecione **Detecção de ameaças**.
1. Selecione **permitir que o Microsoft defender para ponto de extremidade acesse meus dados** e selecione **salvar**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Habilitar a integração entre a central de segurança do Azure e a solução EDR da Microsoft, Microsoft defender para ponto de extremidade":::

    A central de segurança do Azure integrará automaticamente seus servidores ao Microsoft defender para ponto de extremidade. A integração pode levar até 24 horas.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Acessar o Microsoft defender para o portal de ponto de extremidade

1. Verifique se a conta de usuário tem as permissões necessárias. [Saiba mais](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Verifique se você tem um proxy ou firewall que está bloqueando o tráfego anônimo. O sensor do defender for Endpoint conecta-se do contexto do sistema; portanto, o tráfego anônimo deve ser permitido. Para garantir o acesso sem impedimento ao defender para o portal de ponto de extremidade, siga as instruções em [habilitar o acesso a URLs de serviço no servidor proxy](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Abra o [portal da central de segurança do Microsoft defender](https://securitycenter.windows.com/). Saiba mais sobre os recursos e ícones do portal, na [visão geral do portal da central de segurança do Microsoft defender](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Enviar um alerta de teste

Para gerar um alerta de teste benigno do Microsoft defender for Endpoint:

1. Crie uma pasta ' C:\test-MDATP-test '.
1. Use a Área de Trabalho Remota para acessar uma VM do Windows Server 2012 R2 ou uma VM do Windows Server 2016.
1. Abra uma janela de linha de comando.
1. No prompt, copie e execute o comando a seguir. A janela do prompt de comando será fechada automaticamente.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Uma janela de prompt de comando com o comando para gerar um alerta de teste.":::

1. Se o comando for bem-sucedido, você verá um novo alerta no painel da central de segurança do Azure e no portal do Microsoft defender para ponto de extremidade. Esse alerta pode levar alguns minutos para aparecer.
1. Para examinar o alerta na central de segurança, acesse **alertas de segurança aviso** de  >  **linha de comando do PowerShell suspeito**.
1. Na janela investigação, selecione o link para acessar o portal do Microsoft defender para ponto de extremidade.


## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Perguntas frequentes sobre o Microsoft defender para ponto de extremidade integrado da central de segurança

- [Quais são os requisitos de licenciamento para o Microsoft defender para ponto de extremidade?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Se já tenho uma licença para o Microsoft defender for Endpoint, posso obter um desconto para o Azure defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Como fazer mudar de uma ferramenta de EDR de terceiros?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Quais são os requisitos de licenciamento para o Microsoft defender para ponto de extremidade?
O defender for Endpoint está incluído sem custo adicional com o **Azure defender para servidores**. Como alternativa, ele pode ser adquirido separadamente para 50 computadores ou mais.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Se já tenho uma licença para o Microsoft defender for Endpoint, posso obter um desconto para o Azure defender?
Se você já tiver uma licença para o Microsoft defender for Endpoint, não precisará pagar por essa parte da sua licença do Azure defender.

Para confirmar seu desconto, entre em contato com a equipe de suporte da central de segurança e forneça a ID do espaço de trabalho, a região e as informações de licença relevantes para cada licença relevante.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Como fazer mudar de uma ferramenta de EDR de terceiros?
As instruções completas para alternar de uma solução de ponto de extremidade não Microsoft estão disponíveis na documentação do Microsoft defender para ponto de extremidade: [visão geral da migração](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Próximas etapas

- [Plataformas e recursos compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md)
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
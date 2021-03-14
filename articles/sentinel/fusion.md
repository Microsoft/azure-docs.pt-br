---
title: Detecção avançada de ataques de multiestágio no Azure Sentinel
description: Use a tecnologia Fusion no Azure Sentinel para reduzir o fadiga de alertas e criar incidentes acionáveis que se baseiam na detecção avançada de ataques de multiestágio.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724278"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Detecção avançada de ataques de multiestágio no Azure Sentinel

> [!IMPORTANT]
> Algumas detecções de fusão (veja as que estão indicadas abaixo) estão atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Usando a tecnologia de fusão com base no aprendizado de máquina, o Azure Sentinel pode detectar automaticamente ataques de várias etapas identificando combinações de comportamentos anormais e atividades suspeitas que são observadas em vários estágios da cadeia de eliminação. Com base nessas descobertas, o Azure Sentinel gera incidentes que, de outra forma, seriam difíceis de detectar. Esses incidentes incluem dois ou mais alertas ou atividades. Por design, esses incidentes são de baixo volume, alta fidelidade e alta gravidade.

Personalizado para seu ambiente, essa tecnologia de detecção não apenas reduz as taxas de falsos positivos, mas também pode detectar ataques com informações limitadas ou ausentes.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuração de detecção avançada de ataque multiestágio

Essa detecção é habilitada por padrão no Azure Sentinel. Para verificar o status ou para desabilitá-lo caso você esteja usando uma solução alternativa para criar incidentes com base em vários alertas, use as seguintes instruções:

1. Se você ainda não fez isso, entre no [portal do Azure](https://portal.azure.com).

1. Navegue até a  >    >  **análise** de configuração do Azure Sentinel

1. Selecione **regras ativas** e localize **detecção avançada de ataque** de várias etapas na coluna **nome** filtrando a lista para o tipo de regra de **fusão** . Verifique a coluna **status** para confirmar se essa detecção está habilitada ou desabilitada.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. Para alterar o status, selecione essa entrada e, na folha **detecção avançada de ataques de multiestágio** , selecione **Editar**.

1. Na folha **Assistente para criação de regras** , a alteração de status é selecionada automaticamente para você, portanto, selecione **Avançar: revisar** e **salvar**. 

 Como o tipo de regra de **fusão** contém apenas uma regra que não pode ser modificada, os modelos de regra não são aplicáveis a esse tipo de regra.

> [!NOTE]
> Atualmente, o Azure Sentinel usa 30 dias de dados históricos para treinar os sistemas de aprendizado de máquina. Esses dados são sempre criptografados usando as chaves da Microsoft à medida que passam pelo pipeline do Machine Learning. No entanto, os dados de treinamento não serão criptografados usando [CMK (chaves gerenciadas pelo cliente)](customer-managed-keys.md) se você tiver habilitado o CMK em seu espaço de trabalho do Azure Sentinel. Para recusar a fusão, navegue até **Azure Sentinel** \> **Configuration** \> **Analytics \> regras ativas \> detecção de ataque avançado de multiestágio** e na coluna **status** , selecione **desabilitar.**

## <a name="attack-detection-scenarios"></a>Cenários de detecção de ataque

A seção a seguir lista os tipos de cenários de correlação, agrupados por classificação de ameaças, que o Azure Sentinel procura usando a tecnologia Fusion.

Conforme mencionado acima, como a Fusion correlaciona vários alertas de segurança de vários produtos para detectar ataques avançados de várias etapas, as detecções de fusão bem-sucedidas são apresentadas como **incidentes de fusão** na página **incidentes** do Azure Sentinel e não como **alertas** na tabela **alertas de segurança** nos **logs**.

Para habilitar esses cenários de detecção de ataque com capacidade de fusão, todas as fontes de dados listadas devem ser ingeridas usando os conectores de dados associados do Azure Sentinel.

> [!NOTE]
> Alguns desses cenários estão em versão **prévia**. Eles serão indicados.

## <a name="compute-resource-abuse"></a>Abuso de recursos de computação

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Várias atividades de criação de VM seguindo o Azure Active Directory de entrada suspeitas
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Acesso inicial, impacto 

**Técnicas Mitre ATT&CK:** Conta válida (T1078), seqüestro de recursos (T1496)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número anormal de VMs foi criado em uma única sessão após uma entrada suspeita em uma conta do Azure AD. Esse tipo de alerta indica, com um alto grau de confiança, que a conta observada na descrição do incidente de fusão foi comprometida e usada para criar novas VMs para fins não autorizados, como executar operações de mineração de criptografia. As permutações de alertas de entrada do Azure AD suspeitos com o alerta de várias atividades de criação de VM são:

- **Viagem impossível a um local atípicos que leva a várias atividades de criação de VM**

- **Evento de entrada de um local desconhecido que leva a várias atividades de criação de VM**

- **Evento de entrada de um dispositivo infectado que leva a várias atividades de criação de VM**

- **Evento de entrada de um endereço IP anônimo que leva a várias atividades de criação de VM**

- **Evento de entrada do usuário com credenciais vazadas que levam a várias atividades de criação de VM**

## <a name="credential-harvesting-new-threat-classification"></a>Coleta de credenciais (nova classificação de ameaça)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Execução da ferramenta de roubo de credenciais maliciosa após a entrada suspeita

**Táticas de Mitre ATT&CK:** Acesso inicial, acesso à credencial

**Técnicas Mitre ATT&CK:** Conta válida (T1078), despejo de credencial do so (T1003)

**Fontes do conector de dados:** Azure Active Directory Identity Protection, Microsoft defender para ponto de extremidade

**Descrição:** Os incidentes de fusão desse tipo indicam que uma ferramenta conhecida de roubo de credenciais foi executada após uma entrada suspeita do Azure AD. Isso fornece uma indicação de alta confiança que a conta de usuário anotada na descrição do alerta foi comprometida e pode ter usado com êxito uma ferramenta como **Mimikatz** para coletar credenciais como chaves, senhas de texto não criptografado e/ou hashes de senha do sistema. As credenciais coletadas podem permitir que um invasor acesse dados confidenciais, escalonar privilégios e/ou mover-se posteriormente pela rede. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de ferramenta contra roubo de credenciais mal-intencionadas são:

- **Viagem impossível a locais atípicoss que levam à execução de ferramenta contra roubo de credenciais mal-intencionadas**

- **Evento de entrada de um local desconhecido que leva à execução da ferramenta de roubo de credenciais mal-intencionadas**

- **Evento de entrada de um dispositivo infectado que leva à execução da ferramenta de roubo de credenciais mal-intencionadas**

- **Evento de entrada de um endereço IP anônimo que leva à execução da ferramenta de roubo de credenciais mal-intencionadas**

- **Evento de entrada do usuário com credenciais vazadas que levam à execução da ferramenta de roubo de credenciais mal-intencionadas**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Atividade suspeita de roubo de credenciais após a entrada suspeita

**Táticas de Mitre ATT&CK:** Acesso inicial, acesso à credencial

**Técnicas Mitre ATT&CK:** Conta válida (T1078), credenciais de armazenamentos de senha (T1555), despejo de credencial do so (T1003)

**Fontes do conector de dados:** Azure Active Directory Identity Protection, Microsoft defender para ponto de extremidade

**Descrição:** Os incidentes de fusão desse tipo indicam que a atividade associada a padrões de roubo de credenciais ocorreu após uma entrada suspeita do Azure AD. Isso fornece uma indicação de alta confiança que a conta de usuário anotada na descrição do alerta foi comprometida e usada para roubar credenciais como chaves, senhas de texto sem formatação, hashes de senha e assim por diante. As credenciais roubadas podem permitir que um invasor acesse dados confidenciais, escalonar privilégios e/ou mover-se posteriormente pela rede. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de atividade de roubo de credenciais são:

- **Viagem impossível a locais atípicoss que levam à atividade suspeita de roubo de credenciais**

- **Evento de entrada de um local não familiar que leva à atividade suspeita de roubo de credenciais**

- **Evento de entrada de um dispositivo infectado que leva à atividade suspeita de roubo de credenciais**

- **Evento de entrada de um endereço IP anônimo que leva à atividade suspeita de roubo de credenciais**

- **Evento de entrada do usuário com credenciais vazadas que levam à atividade suspeita de roubo de credenciais**

## <a name="crypto-mining-new-threat-classification"></a>Criptografia-mineração (nova classificação de ameaça)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Atividade de mineração de criptografia após a entrada suspeita

**Táticas de Mitre ATT&CK:** Acesso inicial, acesso à credencial

**Técnicas Mitre ATT&CK:** Conta válida (T1078), seqüestro de recursos (T1496)

**Fontes do conector de dados:** Azure Active Directory Identity Protection, Azure defender (central de segurança do Azure)

**Descrição:** Os incidentes de fusão desse tipo indicam a atividade de mineração de criptografia associada a uma entrada suspeita a uma conta do Azure AD. Isso fornece uma indicação de alta confiança que a conta de usuário anotada na descrição do alerta foi comprometida e usada para seqüestrar recursos em seu ambiente para a minha moeda de criptografia. Isso pode privar seus recursos de capacidade de computação e/ou resultar em faturas de uso de nuvem significativamente mais altas do que o esperado. As permutações de alertas de entrada do Azure AD suspeitos com o alerta de atividade de mineração de criptografia são:  

- **Viagem impossível a locais atípicoss que levam à atividade de mineração de criptografia**

- **Evento de entrada de um local desconhecido que leva à atividade de mineração de criptografia**

- **Evento de entrada de um dispositivo infectado que leva à atividade de mineração de criptografia**

- **Evento de entrada de um endereço IP anônimo que leva à atividade de mineração de criptografia**

- **Evento de entrada do usuário com credenciais vazadas que levam à atividade de mineração de criptografia**

## <a name="data-exfiltration"></a>Exfiltração dos dados

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Vazamento caixa de correio do Office 365 seguindo uma entrada suspeita do Azure AD

**Táticas de Mitre ATT&CK:** Acesso inicial, vazamento, coleção

**Técnicas Mitre ATT&CK:** Conta válida (T1078), coleta de email (T1114), vazamento automatizada (T1020)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que uma regra de encaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um usuário após uma entrada suspeita em uma conta do Azure AD. Essa indicação fornece alta confiança de que a conta do usuário (anotada na descrição do incidente do Fusion) foi comprometida e que foi usada para exfiltrar dados da rede da sua organização, habilitando uma regra de encaminhamento de caixa de correio sem o conhecimento do usuário verdadeiro. As permutações de alertas de entrada do Azure AD suspeitos com o alerta de vazamento de caixa de correio do Office 365 são:

- **Viagem impossível a um local atípicos que leva à caixa de correio vazamento do Office 365**

- **Evento de entrada de um local desconhecido que leva à caixa de correio do Office 365 vazamento**

- **Evento de entrada de um dispositivo infectado que leva à caixa de correio do Office 365 vazamento**

- **Evento de entrada de um endereço IP anônimo que leva à caixa de correio do Office 365 vazamento**

- **Evento de entrada do usuário com credenciais vazadas que levam à caixa de correio do Office 365 vazamento**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Download de arquivo em massa seguindo a entrada suspeita do Azure AD

**Táticas de Mitre ATT&CK:** Acesso inicial, vazamento

**Técnicas Mitre ATT&CK:** Conta válida (T1078)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número anormal de arquivos foi baixado por um usuário após uma entrada suspeita em uma conta do Azure AD. Essa indicação fornece alta confiança de que a conta observada na descrição do incidente de fusão foi comprometida e foi usada para exfiltrar dados da rede da sua organização. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de download de arquivo em massa são:  

- **Viagem impossível a um local atípicos que leva ao download de arquivos em massa**

- **Evento de entrada de um local desconhecido que leva ao download de arquivo em massa**

- **Evento de entrada de um dispositivo infectado que leva ao download de arquivo em massa**

- **Evento de entrada de um IP anônimo que leva ao download de arquivo em massa**

- **Evento de entrada do usuário com credenciais vazadas levando ao download de arquivo em massa**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Compartilhamento de arquivos em massa após o logon suspeito do Azure AD

**Táticas de Mitre ATT&CK:** Acesso inicial, vazamento

**Técnicas Mitre ATT&CK:** Conta válida (T1078), vazamento over Web Service (T1567)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número de arquivos acima de um limite específico foi compartilhado para outras pessoas após uma entrada suspeita em uma conta do Azure AD. Essa indicação fornece alta confiança de que a conta observada na descrição do incidente de fusão foi comprometida e usada para exfiltrar dados da rede da sua organização, compartilhando arquivos como documentos, planilhas, etc., com usuários não autorizados para fins mal-intencionados. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de compartilhamento de arquivos em massa são:  

- **Viagem impossível a um local atípicos que leva ao compartilhamento de arquivos em massa**

- **Evento de entrada de um local desconhecido que leva ao compartilhamento de arquivos em massa**

- **Evento de entrada de um dispositivo infectado que leva ao compartilhamento de arquivos em massa**

- **Evento de entrada de um endereço IP anônimo que leva ao compartilhamento de arquivos em massa**

- **Evento de entrada do usuário com credenciais vazadas que levam ao compartilhamento de arquivos em massa**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Regras de manipulação de caixa de entrada suspeitas definidas após o logon suspeito do Azure AD
Este cenário pertence a duas classificações de ameaças nesta lista: **Data vazamento** e **movimento lateral**. Para fins de clareza, ele aparece em ambas as seções.

Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Acesso inicial, movimento lateral, vazamento

**Técnicas Mitre ATT&CK:** Conta válida (T1078), spear phishing interno (T1534)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que as regras de caixa de entrada anômalas foram definidas na caixa de entrada de um usuário após uma entrada suspeita em uma conta do Azure AD. Isso fornece uma indicação de alta confiança de que a conta observada na descrição do incidente de fusão foi comprometida e foi usada para manipular as regras de caixa de entrada de email do usuário para fins mal-intencionados. Isso pode ser uma tentativa de um invasor exfiltrar dados da rede da organização. Como alternativa, o invasor pode estar tentando gerar emails de phishing de dentro da organização (ignorando mecanismos de detecção de phishing destinados a emails de fontes externas) com a finalidade de mover-se mais tarde, obtendo acesso a usuários adicionais e/ou contas privilegiadas. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de regras de manipulação de caixa de entrada suspeita são:  

- **Viagem impossível a um local atípicos que leva à regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de um local desconhecido que leva à regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de um dispositivo infectado que leva à regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de um endereço IP anônimo que leva à regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada do usuário com credenciais vazadas que levam à regra de manipulação de caixa de entrada suspeita**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Várias Power BI atividades de compartilhamento de relatório após a entrada suspeita do Azure AD 
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Acesso inicial, vazamento 

**Técnicas Mitre ATT&CK:** Conta válida (T1078), vazamento over Web Service (T1567)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número anormal de relatórios de Power BI foram compartilhados em uma única sessão após uma entrada suspeita em uma conta do Azure AD. Essa indicação fornece alta confiança de que a conta observada na descrição do incidente de fusão foi comprometida e foi usada para exfiltrar dados da rede da sua organização, compartilhando Power BI relatórios com usuários não autorizados para fins mal-intencionados. As permutas de alertas de entrada do Azure AD suspeitos com as várias Power BI atividades de compartilhamento de relatório são:  

- **Viagem impossível a um local atípicos que leva a várias atividades de compartilhamento de relatório Power BI**

- **Evento de entrada de um local desconhecido que leva a vários Power BI atividades de compartilhamento de relatório**

- **Evento de entrada de um dispositivo infectado que leva a várias Power BI atividades de compartilhamento de relatório**

- **Evento de entrada de um endereço IP anônimo que leva a várias Power BI atividades de compartilhamento de relatório**

- **Evento de entrada do usuário com credenciais vazadas que levam a várias Power BI atividades de compartilhamento de relatório**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Suspeita de Power BI compartilhamento de relatório seguindo o logon suspeito do Azure AD
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Acesso inicial, vazamento 

**Técnicas Mitre ATT&CK:** Conta válida (T1078), vazamento over Web Service (T1567)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que uma atividade de compartilhamento de relatório Power BI suspeita ocorreu após uma entrada suspeita em uma conta do Azure AD. A atividade de compartilhamento foi identificada como suspeita porque o relatório de Power BI continha informações confidenciais identificadas usando o processamento de linguagem natural e porque ele foi compartilhado com um endereço de email externo, publicado na Web ou entregue como um instantâneo para um endereço de email assinado externamente. Esse alerta indica com alta confiança que a conta observada na descrição do incidente de fusão foi comprometida e foi usada para exfiltrar dados confidenciais de sua organização, compartilhando Power BI relatórios com usuários não autorizados para fins mal-intencionados. As permutações de alertas de entrada suspeitos do Azure AD com o compartilhamento de relatório suspeito Power BI são:  

- **Viagem impossível a um local atípicos que leva a um Power BI de compartilhamento de relatório suspeito**

- **Evento de entrada de um local desconhecido que leva a suspeitas de Power BI compartilhamento de relatório**

- **Evento de entrada de um dispositivo infectado que leva a um Power BI de compartilhamento de relatório suspeito**

- **Evento de entrada de um endereço IP anônimo que leva a suspeitas de Power BI compartilhamento de relatório**

- **Evento de entrada do usuário com credenciais vazadas levando a suspeitas de Power BI compartilhamento de relatório**

## <a name="data-destruction"></a>Destruição de dados

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Exclusão de arquivo em massa após entrada suspeita do Azure AD

**Táticas de Mitre ATT&CK:** Acesso inicial, impacto

**Técnicas Mitre ATT&CK:** Conta válida (T1078), destruição de dados (T1485)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número anormal de arquivos exclusivos foi excluído após uma entrada suspeita em uma conta do Azure AD. Isso fornece uma indicação de que a conta observada na descrição do incidente de fusão pode ter sido comprometida e usada para destruir dados para fins mal-intencionados. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de exclusão de arquivo em massa são:  

- **Viagem impossível a um local atípicos que leva à exclusão em massa de arquivos**

- **Evento de entrada de um local não familiar que leva à exclusão de arquivos em massa**

- **Evento de entrada de um dispositivo infectado que leva à exclusão em massa de arquivos**

- **Evento de entrada de um endereço IP anônimo que leva à exclusão de arquivos em massa**

- **Evento de entrada do usuário com credenciais vazadas levando à exclusão de arquivos em massa**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Atividade de exclusão de email suspeita após entrada suspeita do Azure AD
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Acesso inicial, impacto 

**Técnicas Mitre ATT&CK:** Conta válida (T1078), destruição de dados (T1485)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número anormal de emails foi excluído em uma única sessão após uma entrada suspeita em uma conta do Azure AD. Isso fornece uma indicação de que a conta observada na descrição do incidente de fusão pode ter sido comprometida e usada para destruir dados para fins mal-intencionados, como prejudicar a organização ou ocultar a atividade de email relacionada ao spam. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de atividade de exclusão de email suspeito são:   

- **Viagem impossível a um local atípicos que leva à atividade de exclusão de email suspeita**

- **Evento de entrada de um local desconhecido que leva à atividade de exclusão de email suspeita**

- **Evento de entrada de um dispositivo infectado que leva à atividade de exclusão de email suspeita**

- **Evento de entrada de um endereço IP anônimo que leva à atividade de exclusão de email suspeito**

- **Evento de entrada do usuário com credenciais vazadas que levam à atividade de exclusão de email suspeita**

## <a name="denial-of-service"></a>Negação de serviço

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Várias atividades de exclusão de VM após o logon suspeito do Azure AD
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Acesso inicial, impacto

**Técnicas Mitre ATT&CK:** Conta válida (T1078), negação de serviço de ponto de extremidade (T1499)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número anormal de VMs foram excluídas em uma única sessão após uma entrada suspeita em uma conta do Azure AD. Essa indicação fornece alta confiança de que a conta observada na descrição do incidente de fusão foi comprometida e usada para tentar interromper ou destruir o ambiente de nuvem da organização. As permutações de alertas de entrada suspeitos do Azure AD com o alerta várias atividades de exclusão de VM são:  

- **Viagem impossível a um local atípicos que leva a várias atividades de exclusão de VM**

- **Evento de entrada de um local desconhecido que leva a várias atividades de exclusão de VM**

- **Evento de entrada de um dispositivo infectado que leva a várias atividades de exclusão de VM**

- **Evento de entrada de um endereço IP anônimo que leva a várias atividades de exclusão de VM**

- **Evento de entrada do usuário com credenciais vazadas que levam a várias atividades de exclusão de VM**

## <a name="lateral-movement"></a>Movimento lateral

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Representação do Office 365 seguindo a entrada suspeita do Azure AD

**Táticas de Mitre ATT&CK:** Acesso inicial, movimento lateral

**Técnicas Mitre ATT&CK:** Conta válida (T1078), spear phishing interno (T1534)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número anormal de ações de representação ocorreu após uma entrada suspeita de uma conta do Azure AD. Em alguns softwares, há opções para permitir que os usuários representem outros usuários. Por exemplo, os serviços de email permitem que os usuários autorizem outros usuários a enviar emails em seu nome. Esse alerta indica maior confiança de que a conta observada na descrição do incidente de fusão foi comprometida e foi usada para conduzir atividades de representação para fins mal-intencionados, como enviar emails de phishing para distribuição de malware ou movimento lateral. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de representação do Office 365 são:  

- **Viagem impossível a um local atípicos que leva à representação do Office 365**

- **Evento de entrada de um local desconhecido que leva à representação do Office 365**

- **Evento de entrada de um dispositivo infectado que leva à representação do Office 365**

- **Evento de entrada de um endereço IP anônimo que leva à representação do Office 365**

- **Evento de entrada do usuário com credenciais vazadas que levam à representação do Office 365**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Regras de manipulação de caixa de entrada suspeitas definidas após o logon suspeito do Azure AD
Este cenário pertence a duas classificações de ameaças nesta lista: **movimentação lateral** e **vazamento de dados**. Para fins de clareza, ele aparece em ambas as seções.

Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Acesso inicial, movimento lateral, vazamento

**Técnicas Mitre ATT&CK:** Conta válida (T1078), spear phishing interno (T1534), vazamento automatizado (T1020)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que as regras de caixa de entrada anômalas foram definidas na caixa de entrada de um usuário após uma entrada suspeita em uma conta do Azure AD. Essa indicação fornece alta confiança de que a conta observada na descrição do incidente de fusão foi comprometida e foi usada para manipular as regras de caixa de entrada de email do usuário para fins mal-intencionados. Isso pode ser uma tentativa de um invasor exfiltrar dados da rede da organização. Como alternativa, o invasor pode estar tentando gerar emails de phishing de dentro da organização (ignorando mecanismos de detecção de phishing destinados a emails de fontes externas) com a finalidade de mover-se mais tarde, obtendo acesso a usuários adicionais e/ou contas privilegiadas. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de regras de manipulação de caixa de entrada suspeita são:

- **Viagem impossível a um local atípicos que leva à regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de um local desconhecido que leva à regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de um dispositivo infectado que leva à regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de um endereço IP anônimo que leva à regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada do usuário com credenciais vazadas que levam à regra de manipulação de caixa de entrada suspeita**

## <a name="malicious-administrative-activity"></a>Atividade administrativa mal-intencionada

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Atividade administrativa suspeita do aplicativo de nuvem após a entrada suspeita do Azure AD

**Táticas de Mitre ATT&CK:** Acesso inicial, persistência, defesa evasão, movimento lateral, coleção, vazamento e impacto

**Técnicas Mitre ATT&CK:** N/A

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que um número anormal de atividades administrativas foi realizado em uma única sessão após uma entrada suspeita do Azure AD da mesma conta. Isso fornece uma indicação de que a conta observada na descrição do incidente de fusão pode ter sido comprometida e usada para fazer qualquer número de ações administrativas não autorizadas com más intenções. Isso também indica que uma conta com privilégios administrativos pode ter sido comprometida. As permutações de alertas de entrada do Azure AD suspeitos com o alerta de atividade administrativa suspeita do aplicativo de nuvem são:  

- **Viagem impossível a um local atípicos que leva à atividade administrativa suspeita do aplicativo de nuvem**

- **Evento de entrada de um local desconhecido que leva à atividade administrativa do aplicativo de nuvem suspeito**

- **Evento de entrada de um dispositivo infectado que leva à atividade administrativa suspeita do aplicativo de nuvem**

- **Evento de entrada de um endereço IP anônimo que leva à atividade administrativa suspeita do aplicativo de nuvem**

- **Evento de entrada do usuário com credenciais vazadas que levam à atividade administrativa do aplicativo de nuvem suspeito**

## <a name="malicious-execution-with-legitimate-process"></a>Execução mal-intencionada com processo legítimo

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>O PowerShell fez uma conexão de rede suspeita, seguida pelo tráfego anômala sinalizado pelo firewall do Palo Alto Networks.
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Chão

**Técnicas Mitre ATT&CK:** Interpretador de comandos e scripts (T1059)

**Fontes do conector de dados:** Microsoft defender para ponto de extremidade (anteriormente chamado de proteção avançada contra ameaças do Microsoft defender ou MDATP), Palo Alto Networks 

**Descrição:** Os incidentes de fusão desse tipo indicam que uma solicitação de conexão de saída foi feita por meio de um comando do PowerShell e, depois disso, a atividade de entrada anômala foi detectada pelo firewall de redes Palo Alto. Isso fornece uma indicação de que um invasor provavelmente obteve acesso à sua rede e está tentando executar ações mal-intencionadas. As tentativas de conexão pelo PowerShell que seguem esse padrão podem ser uma indicação de atividade de controle e comando de malware, solicitações de download de malware adicional ou um invasor que estabelece acesso interativo remoto. Assim como acontece com todos os ataques "viver com o terreno", essa atividade pode ser um uso legítimo do PowerShell. No entanto, a execução do comando do PowerShell seguida por atividade suspeita de firewall de entrada aumenta a confiança de que o PowerShell está sendo usado de maneira mal-intencionada e deve ser investigado mais detalhadamente. Nos logs do Palo Alto, o Azure Sentinel se concentra nos [logs de ameaças](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando ameaças são permitidas (dados suspeitos, arquivos, inundações, pacotes, exames, spyware, URLs, vírus, vulnerabilidades, incêndio-vírus, Wildfires). Também faça referência ao log de ameaças do Palo Alto correspondente ao [tipo de risco/ameaça](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de fusão para obter detalhes adicionais do alerta.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Execução de WMI remota suspeita seguida pelo tráfego anormal sinalizado pelo firewall do Palo Alto Networks
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Execução, descoberta

**Técnicas Mitre ATT&CK:** Instrumentação de Gerenciamento do Windows (T1047)

**Fontes do conector de dados:** Microsoft defender para ponto de extremidade (anteriormente MDATP), Palo Alto Networks 

**Descrição:** Os incidentes de fusão desse tipo indicam que os comandos da interface de gerenciamento do Windows (WMI) foram executados remotamente em um sistema e, depois disso, a atividade de entrada suspeita foi detectada pelo firewall do Palo Alto Networks. Isso fornece uma indicação de que um invasor pode ter obtido acesso à sua rede e está tentando mover-se mais tarde, escalonar privilégios e/ou executar cargas mal-intencionadas. Assim como acontece com todos os ataques "viver com o terreno", essa atividade pode ser um uso legítimo do WMI. No entanto, a execução do comando WMI remoto seguida por atividade suspeita de firewall de entrada aumenta a confiança de que o WMI está sendo usado de maneira mal-intencionada e deve ser investigado mais detalhadamente. Nos logs do Palo Alto, o Azure Sentinel se concentra nos [logs de ameaças](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando ameaças são permitidas (dados suspeitos, arquivos, inundações, pacotes, exames, spyware, URLs, vírus, vulnerabilidades, incêndio-vírus, Wildfires). Também faça referência ao log de ameaças do Palo Alto correspondente ao [tipo de risco/ameaça](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de fusão para obter detalhes adicionais do alerta.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Linha de comando suspeita do PowerShell após a entrada suspeita

**Táticas de Mitre ATT&CK:** Acesso inicial, execução

**Técnicas Mitre ATT&CK:** Conta válida (T1078), interpretador de comando e script (T1059)

**Fontes do conector de dados:** Azure Active Directory Identity Protection, Microsoft defender for Endpoint (anteriormente MDATP)

**Descrição:** Os incidentes de fusão desse tipo indicam que um usuário executou comandos do PowerShell potencialmente mal-intencionados após uma entrada suspeita em uma conta do Azure AD. Isso fornece uma indicação de alta confiança de que a conta observada na descrição do alerta foi comprometida e outras ações mal-intencionadas foram tomadas. Os invasores geralmente aproveitam o PowerShell para executar cargas mal-intencionadas na memória sem deixar artefatos no disco, a fim de evitar a detecção por mecanismos de segurança baseados em disco, como scanners de vírus. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de comando do PowerShell suspeito são:

- **Viagem impossível a locais atípicoss que levam à linha de comando suspeita do PowerShell**

- **Evento de entrada de um local desconhecido que leva à linha de comando do PowerShell suspeita**

- **Evento de entrada de um dispositivo infectado que leva à linha de comando do PowerShell suspeita**

- **Evento de entrada de um endereço IP anônimo que leva à linha de comando do PowerShell suspeita**

- **Evento de entrada do usuário com credenciais vazadas que levam à linha de comando do PowerShell suspeita**

## <a name="malware-c2-or-download"></a>Malware C2 ou download

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Solicitação de rede para TOR o serviço de anonimato seguido pelo tráfego anormal sinalizado pelo firewall do Palo Alto Networks.
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Comando e controle

**Técnicas Mitre ATT&CK:** Canal criptografado (T1573), proxy (T1090)

**Fontes do conector de dados:** Microsoft defender para ponto de extremidade (anteriormente MDATP), Palo Alto Networks 

**Descrição:** Os incidentes de fusão desse tipo indicam que uma solicitação de conexão de saída foi feita ao serviço de anonimato TOR, e depois disso, a atividade de entrada anômala foi detectada pelo firewall do Palo Alto Networks. Isso fornece uma indicação de que um invasor provavelmente obteve acesso à sua rede e está tentando ocultar suas ações e suas intenções. As conexões com a rede TOR seguindo esse padrão podem ser uma indicação de atividade de controle e comando de malware, solicitações de download de malware adicional ou um invasor que estabelece acesso interativo remoto. Nos logs do Palo Alto, o Azure Sentinel se concentra nos [logs de ameaças](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando ameaças são permitidas (dados suspeitos, arquivos, inundações, pacotes, exames, spyware, URLs, vírus, vulnerabilidades, incêndio-vírus, Wildfires). Também faça referência ao log de ameaças do Palo Alto correspondente ao [tipo de risco/ameaça](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de fusão para obter detalhes adicionais do alerta.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Conexão de saída com o IP com um histórico de tentativas de acesso não autorizado seguido pelo tráfego anormal sinalizado pelo firewall do Palo Alto Networks
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Comando e controle

**Técnicas Mitre ATT&CK:** Não aplicável

**Fontes do conector de dados:** Microsoft defender para ponto de extremidade (anteriormente MDATP), Palo Alto Networks 

**Descrição:** Os incidentes de fusão desse tipo indicam que uma conexão de saída com um endereço IP com um histórico de tentativas de acesso não autorizadas foi estabelecida e, depois disso, a atividade anômala foi detectada pelo firewall de redes Palo Alto. Isso fornece uma indicação de que um invasor provavelmente obteve acesso à sua rede. As tentativas de conexão após esse padrão podem ser uma indicação de atividade de controle e comando de malware, solicitações de download de malware adicional ou um invasor que estabelece acesso interativo remoto. Nos logs do Palo Alto, o Azure Sentinel se concentra nos [logs de ameaças](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando ameaças são permitidas (dados suspeitos, arquivos, inundações, pacotes, exames, spyware, URLs, vírus, vulnerabilidades, incêndio-vírus, Wildfires). Também faça referência ao log de ameaças do Palo Alto correspondente ao [tipo de risco/ameaça](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de fusão para obter detalhes adicionais do alerta.

## <a name="ransomware"></a>Ransomware

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Execução de ransomware após o logon suspeito do Azure AD

**Táticas de Mitre ATT&CK:** Acesso inicial, impacto

**Técnicas Mitre ATT&CK:** Conta válida (T1078), dados criptografados para impacto (T1486)

**Fontes do conector de dados:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrição:** Os incidentes de fusão desse tipo indicam que o comportamento anormal do usuário que indica um ataque de ransomware foi detectado após uma entrada suspeita em uma conta do Azure AD. Essa indicação fornece alta confiança de que a conta observada na descrição do incidente de fusão foi comprometida e foi usada para criptografar dados para fins de desdelitor o proprietário dos dados ou negar o acesso do proprietário dos dados aos seus dados. As permutações de alertas de entrada suspeitos do Azure AD com o alerta de execução de ransomware são:  

- **Viagem impossível a um local atípicos que leva a ransomware no aplicativo em nuvem**

- **Evento de entrada de um local desconhecido que leva ao ransomware no aplicativo de nuvem**

- **Evento de entrada de um dispositivo infectado que leva ao ransomware no aplicativo de nuvem**

- **Evento de entrada de um endereço IP anônimo que leva ao ransomware no aplicativo de nuvem**

- **Evento de entrada do usuário com credenciais vazadas que levam ao ransomware no aplicativo de nuvem**

## <a name="remote-exploitation"></a>Exploração remota

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Uso suspeito da estrutura de ataque seguida pelo tráfego anormal sinalizado pelo firewall do Palo Alto Networks
Este cenário está atualmente em **Visualização**.

**Táticas de Mitre ATT&CK:** Acesso inicial, execução, movimentação lateral, elevação de privilégio

**Técnicas Mitre ATT&CK:** Exploração de Public-Facing aplicativo (T1190), exploração de execução de cliente (T1203), exploração de serviços remotos (T1210), exploração de elevação de privilégio (T1068)

**Fontes do conector de dados:** Microsoft defender para ponto de extremidade (anteriormente MDATP), Palo Alto Networks 

**Descrição:** Os incidentes de fusão desse tipo indicam que usos não padrão de protocolos, que se assemelham ao uso de estruturas de ataque como o Metasploit, foram detectados e, depois disso, a atividade de entrada suspeita foi detectada pelo firewall de redes Palo Alto. Essa pode ser uma indicação inicial de que um invasor explorou um serviço para obter acesso aos seus recursos de rede ou que um invasor já obteve acesso e está tentando explorar ainda mais os sistemas/serviços disponíveis para mover-se mais tarde e/ou escalonar privilégios. Nos logs do Palo Alto, o Azure Sentinel se concentra nos [logs de ameaças](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando ameaças são permitidas (dados suspeitos, arquivos, inundações, pacotes, exames, spyware, URLs, vírus, vulnerabilidades, incêndio-vírus, Wildfires). Também faça referência ao log de ameaças do Palo Alto correspondente ao [tipo de risco/ameaça](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de fusão para obter detalhes adicionais do alerta.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu mais sobre a detecção avançada de ataques de multiestágio, talvez esteja interessado no início rápido a seguir para saber como obter visibilidade de seus dados e ameaças potenciais: comece a usar o [Azure Sentinel](quickstart-get-visibility.md).

Se você estiver pronto para investigar os incidentes que são criados para você, consulte o seguinte tutorial: [investigar incidentes com o Azure Sentinel](tutorial-investigate-cases.md).


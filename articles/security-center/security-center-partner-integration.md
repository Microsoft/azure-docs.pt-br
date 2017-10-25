---
title: "Integração de parceiros e soluções na Central de Segurança do Azure | Microsoft Docs"
description: "Saiba como a Central de Segurança do Azure se integra aos parceiros para aprimorar a segurança geral dos recursos do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: a6998c997840f1a9f349b85a4274908b611cd315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Integração de parceiros e soluções na Central de Segurança do Azure

Neste artigo, descreveremos como a Central de Segurança do Azure se integra aos parceiros para ajudá-lo a melhorar a segurança geral. A Central de Segurança oferece uma experiência integrada no Azure e aproveita o Azure Marketplace para realizar a certificação de parceiro e a cobrança.

## <a name="deploy-partner-solutions-from-security-center"></a>Implantar soluções de parceiro da Central de Segurança

Existem quatro motivos principais para utilizar a integração de parceiros na Central de Segurança:

- **Facilidade de implantação**. Implantar uma solução de parceiro seguindo a recomendação da Central de Segurança é muito mais fácil. O processo de implantação pode ser completamente automatizado por meio de uma topologia de rede e de configuração padrão. Como alternativa, os clientes podem escolher uma opção semiautomatizada para obter mais flexibilidade e personalização.
- **Detecções integradas**. Os eventos de segurança das soluções de parceiro são automaticamente coletados, agregados e exibidos como parte dos incidentes e alertas da Central de Segurança. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Monitoramento e gerenciamento de integridade unificados**. Os clientes podem usar eventos de integridade integrados para monitorar todas as soluções de parceiros em um relance. O gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.
- **Exportar para o SIEM**. Os clientes podem exportar todos os alertas de parceiro e da Central de Segurança no Formato de Evento Comum (CEF) para sistemas de Gerenciamento de Informações e Eventos de Segurança (SIEM) locais usando a integração de log do Azure (versão prévia).


## <a name="partners-that-integrate-with-security-center"></a>Parceiros que se integram à Central de Segurança

No momento, a integração nativa das soluções de parceiros que estão disponíveis no Azure Marketplace com a Central de Segurança são:

- **Proteção do ponto de extremidade**. [Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec e [Antimalware da Microsoft para Serviços de Nuvem e Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware).
- **Firewall do aplicativo Web**. [Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [Gateway de Aplicativo do Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/). 
- **Firewall de última geração**. [Ponto de verificação](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html). 
- **Avaliação de vulnerabilidade**. [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/). 

Ao longo do tempo, a Central de Segurança expandirá o número de parceiros dentro dessas categorias e adicionará novas categorias. 

## <a name="deploy-a-partner-solution"></a>Implantar uma solução de parceiro

Com base na configuração de seu ambiente do Azure e na política de segurança que você definiu, a Central de Segurança pode recomendar a implantação de uma solução de parceiro. A recomendação da Central de Segurança orienta você pelo processo de seleção e instalação de uma solução de parceiro. A experiência geral de implantação pode variar, dependendo do tipo de solução e do parceiro que você usar. Para obter mais informações, consulte os seguintes artigos:

- [Instalar o Endpoint Protection](security-center-install-endpoint-protection.md)
- [Adicione um firewall do aplicativo Web](security-center-add-web-application-firewall.md)
- [Adicionar um firewall de próxima geração](security-center-add-next-generation-firewall.md)
- [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Gerenciar soluções de parceiros

Após a implantação, para exibir informações sobre a integridade da solução e executar o gerenciamento básico de tarefas, no painel **Central de Segurança**, selecione **Soluções de parceiro**.

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

O conteúdo que você vê quando abre as Soluções de Segurança pode variar de acordo com sua infraestrutura. Usando a imagem anterior como exemplo, esta página possui três seções:

- **Soluções conectadas**. Exibe soluções conectadas à Central de Segurança.
- **Soluções descobertas**. Exibe soluções que não estão conectadas à Central de Segurança. Você pode conectar essas soluções, e elas serão exibidas em **Soluções conectadas**. Se a Central de Segurança não detectar soluções desconectadas, essa seção ficará oculta.
- **Adicionar fontes de dados**. Exibe fontes de dados do Azure e de outros que você pode adicionar à Central de Segurança.

### <a name="connected-solutions"></a>Soluções conectadas

A seção **Soluções conectadas** mostra todas as soluções de segurança que estão conectadas atualmente à Central de Segurança. 

![Soluções conectadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

As informações que você vê podem variar de acordo com a solução. Algumas informações disponíveis em cada bloco podem incluir:

- **Ícone da empresa parceira**. Se a Central de Segurança não possui o ícone da empresa, os primeiros caracteres do nome do parceiro são exibidos.
- **Tipo de solução**. O tipo de solução é exibido.
- **Nome do computador**. O nome do computador é exibido.
- **Status de integridade**. Se um indicador de integridade não for enviado, a Central de Segurança mostrará a data e a hora do último evento recebido para indicar se o dispositivo está se comunicando ou não. Se a Central de Segurança não receber o indicador de integridade de uma solução específica, o bloco da solução não aparecerá nessa seção.

> [!NOTE]
> A Central de Segurança mostra a data e a hora do último evento recebido para indicar se o dispositivo está se comunicando ou não. As soluções que não estiverem enviando o indicador de integridade serão exibidas como conectadas se estiverem em alerta ou se houve envio de eventos nos últimos 14 dias.
>  

Algumas dessas soluções podem estar totalmente integradas ao Azure; outras podem ser locais. Como a Central de Segurança dá suporte a [CEF](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef), ela pode se conectar a soluções que usam CEF, por exemplo, um firewall que dá suporte a CEF. Depois que essa solução é adicionada à Central de Segurança, o firewall envia logs em formato CEF à Central de Segurança, que os repassa ao [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview). O firewall é um recurso não pertencente ao Azure que envia eventos, mas não indicadores de integridade. A única informação que a Central de Segurança tem sobre integridade é a última vez que o dispositivo enviou um evento. Para todos os recursos que não são do Azure, na área de integridade do bloco, a Central de Segurança exibe a data e a hora do último evento recebido. Essa informação indicam que o recurso não Azure ainda está se comunicando.

### <a name="discovered-solutions"></a>Soluções descobertas

A seção **Soluções descobertas** mostra todas as soluções que foram adicionadas por meio do Azure. Ela também mostra todas as soluções sugeridas pela Central de Segurança para serem conectadas.

![Soluções descobertas](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

A Central de Segurança pode se integrar a soluções do Azure internas, como o [Azure AD (Azure Active Directory) Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection). Se você tem uma licença do Azure AD Identity Protection, mas o produto não está conectado a Central de Segurança, ele será listado nas **Soluções descobertas**. Para integrar essa solução à Central de Segurança, selecione **CONECTAR** no bloco **Azure AD Identity Protection** e a página abaixo será exibida:

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Para concluir a conexão do Azure AD Identity Protection, selecione um espaço de trabalho onde os dados serão salvos. Todos os dados do Azure AD Identity Protection virão da região do espaço de trabalho que foi selecionada nesta etapa. Use o seletor de espaço de trabalho para selecionar o espaço de trabalho, e os dados começarão a fluir para ele.

Para se conectar à Central de Segurança, você deve ser um administrador global ou um administrador de segurança. Se você não tiver permissões, o botão **Conectar-se** será desabilitado. Uma mensagem será exibida explicando por que o botão está desabilitado.

Os alertas do Azure AD Identity Protection percorrem o pipe de detecção da Central de Segurança. Dessa forma, você obtém alertas da Central de Segurança e do Azure AD Identity Protection. A Central de Segurança mescla todos os alertas que parecem relevantes para criar um [incidente de segurança](https://docs.microsoft.com/azure/security-center/security-center-incident). A descrição do incidente de segurança oferece mais controle sobre atividades suspeitas.

### <a name="add-data-sources"></a>Adicionar fontes de dados

Você pode adicionar computadores Azure e não Azure para integração com o Security Center. Adicionar computadores não Azure significa que você pode adicionar computadores locais ou um dispositivo que dê suporte a CEF. 

![Fontes de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>Consulte também

Neste artigo, você aprendeu a integrar as soluções de parceiro à Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Guia de planejamento e operações da Central de Segurança](security-center-planning-and-operations-guide.md)
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas de segurança por tipo na Central de Segurança](security-center-alerts-type.md)
* [Monitoramento da integridade de segurança na Central de Segurança](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Monitoramento das soluções de parceiros na Central de Segurança](security-center-partner-solutions.md). Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Obtenha respostas para perguntas frequentes sobre como usar a Central de Segurança.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.

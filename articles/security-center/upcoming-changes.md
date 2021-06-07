---
title: Alterações importantes chegando à Central de Segurança do Azure
description: Futuras alterações na Central de Segurança do Azure às quais você talvez precise estar atento e que podem exigir algum planejamento
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 6204be2ff52b8aac89b93ac09337b1560255e11d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491875"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Futuras alterações importantes na Central de Segurança do Azure

> [!IMPORTANT]
> As informações nesta página estão relacionadas aos produtos ou recursos de pré-lançamento e podem ser substancialmente modificadas antes de serem lançadas comercialmente, caso sejam. A Microsoft não assume nenhum compromisso nem faz garantias, expressas nem implícitas, quanto às informações fornecidas daqui em diante.

Nesta página, você aprenderá sobre as alterações planejadas para a Central de Segurança. Ela descreve as modificações planejadas para o produto que podem afetar coisas como a classificação de segurança ou os fluxos de trabalho.

Se você estiver procurando as notas sobre a versão mais recentes, poderá encontrá-las no artigo [Novidades na Central de Segurança do Azure](release-notes.md).


## <a name="planned-changes"></a>Alterações planejadas

| Alteração planejada                                                                                                                                                        | Data estimada da alteração |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [Vinte e uma recomendações estão sendo transferidas entre controles de segurança](#21-recommendations-moving-between-security-controls)                                                           | Abril de 2021                |
| [Duas recomendações do controle de segurança "Aplicar atualizações do sistema" que está sendo preterido](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | Abril de 2021                |
| [As recomendações da AWS serão lançadas em GA (disponibilidade geral)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | Abril de 2021                |
| [Aprimoramentos na recomendação de classificação de dados SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | T2 de 2021                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>Vinte e uma recomendações estão sendo transferidas entre controles de segurança 

**Data estimada da alteração:** abril de 2021

As recomendações a seguir estão sendo transferidas para um controle de segurança diferente. Os controles de segurança são grupos lógicos de recomendações de segurança relacionadas e refletem superfícies vulneráveis a ataques. Essa transferência garantirá que cada uma dessas recomendações esteja no controle mais apropriado para atender ao respectivo objetivo. 

Saiba quais recomendações estão em cada controle de segurança em Controles de segurança e suas recomendações.

|Recomendação |Alteração e respectivo impacto  |
|---------|---------|
|A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL<br>A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL<br>As vulnerabilidades encontradas nos bancos de dados SQL deverão ser corrigidas novamente<br>As vulnerabilidades nos seus bancos de dados SQL em VMs devem ser corrigidas     |Como executar uma transferência da opção Corrigir vulnerabilidades (vale seis pontos)<br>para Corrigir configurações de segurança (vale quatro pontos).<br>Essas recomendações terão um impacto reduzido em sua pontuação, dependendo do ambiente.|
|Deve haver mais de um proprietário atribuído à sua assinatura<br>As variáveis da conta de automação devem ser criptografadas<br>Dispositivos IoT – O processo auditado parou de enviar eventos<br>Dispositivos IoT – Houve uma falha na validação de linha de base do sistema operacional<br>Dispositivos IoT – É preciso atualizar o pacote de criptografia do TLS<br>Dispositivos IoT – Abrir portas no dispositivo<br>Dispositivos IoT – Uma política de firewall permissiva foi encontrada em uma das cadeias<br>Dispositivos IoT – Uma regra de firewall permissiva foi encontrada na cadeia de entrada<br>Dispositivos IoT – Uma regra de firewall permissiva foi encontrada na cadeia de saída<br>Os logs de diagnóstico no Hub IoT devem ser habilitados<br>Dispositivos IoT – O agente está enviando mensagens subutilizadas<br>Dispositivos IoT – A política de filtro IP padrão deverá ser negada<br>Dispositivos IoT – A regra de filtro IP é maior do que o intervalo de IP<br>Dispositivos IoT – Os intervalos e o tamanho das mensagens do agente deverão ser ajustados<br>Dispositivos IoT – As credenciais de autenticação são idênticas<br>Dispositivos IoT – O processo auditado parou de enviar eventos<br>Dispositivos IoT – A configuração de linha de base do SO (sistema operacional) deverá ser corrigida|Como executar uma transferência para a opção **Implementar práticas recomendadas de segurança**.<br>Ao transferir uma recomendação para a opção Implementar o controle de segurança de práticas recomendadas de segurança, que não vale pontos, a recomendação não afetará mais sua classificação de segurança.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas recomendações do controle de segurança "Aplicar atualizações do sistema" que está sendo preterido

**Data estimada da alteração:** abril de 2021

As duas recomendações abaixo estão sendo preteridas:

- **A versão do sistema operacional das funções de serviço de nuvem deverá ser atualizada** – Por padrão, o Azure atualiza de modo periódico o SO convidado para obter a imagem compatível mais recente dentro da família de sistemas operacionais especificada em sua configuração de serviço (.cscfg), como o Windows Server 2016.
- **Os Serviços de Kubernetes deverão ser atualizados para uma versão do Kubernetes não vulnerável** – Esta avaliação da recomendação não é tão abrangente quanto gostaríamos. A versão atual dessa recomendação será substituída em algum momento por uma versão aprimorada e mais alinhada com as necessidades de segurança do nosso cliente.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>As recomendações da AWS serão lançadas em GA (disponibilidade geral)

**Data estimada da alteração:** abril de 2021

A Central de Segurança do Azure protege as cargas de trabalho no Azure, na AWS (Amazon Web Services) e no GCP (Google Cloud Platform).

As recomendações provenientes do AWS Security Hub estão em versão prévia desde que os conectores de nuvem foram introduzidos. As recomendações sinalizadas como **Versão prévia** não estão incluídas nos cálculos da sua classificação de segurança, mas ainda devem ser corrigidas sempre que possível, de modo que, quando o período de versão prévia terminar, elas contribuam para a sua classificação.

Com essa alteração, dois conjuntos de recomendações da AWS passarão para GA:

- [Controles do PCI DSS do Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Controles do CIS AWS Foundations Benchmark do Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Quando eles passarem para GA e as avaliações forem executadas nos seus recursos da AWS, os resultados afetarão sua classificação de segurança combinada referente a todos os seus recursos de nuvem híbrida e multinuvem. 



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Aprimoramentos na recomendação de classificação de dados SQL

**Data estimada da alteração:** T2 de 2021

A recomendação **Os dados confidenciais nos seus bancos de dados SQL devem ser classificados** no controle de segurança **Aplicar classificação de dados** será substituída por uma nova versão que está mais bem alinhada com a estratégia de classificação de dados da Microsoft. Como resultado, a ID da recomendação também será alterada (atualmente, b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Próximas etapas

Para ver todas as alterações recentes feitas no produto, confira as [Novidades na Central de Segurança do Azure](release-notes.md).
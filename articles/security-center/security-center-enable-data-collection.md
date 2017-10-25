---
title: "Coleta de dados na Central de Segurança do Azure | Microsoft Docs"
description: " Saiba como habilitar a coleta de dados na Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-collection-in-azure-security-center"></a>Coleta de dados na Central de Segurança do Azure
A Central de Segurança coleta dados de suas VMs (máquinas virtuais) do Azure e dos computadores não Azure a fim de monitorar as ameaças e vulnerabilidades de segurança. Os dados são coletados usando o Microsoft Monitoring Agent, que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados em seu espaço de trabalho para serem analisados. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP, usuário registrado e ID do locatário. O Microsoft Monitoring Agent também copia os arquivos de despejo de memória para seu espaço de trabalho.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Habilitar o provisionamento automático do Microsoft Monitoring Agent     
Quando o provisionamento automático está habilitado, a Central de Segurança provisiona o Microsoft Monitoring Agent em todas as VMs do Azure com suporte, bem como em quaisquer novas VMs que forem criadas. O provisionamento automático é altamente recomendável e é necessário para as assinaturas na camada Standard da Central de Segurança.

> [!NOTE]
> Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos. Para obter mais informações, consulte [desabilitar o provisionamento automático](security-center-enable-data-collection.md#disable-automatic-provisioning) neste artigo. Os instantâneos de disco da VM e a coleção de artefatos ficam habilitados mesmo que o provisionamento automático esteja desabilitado.
>
>

Para habilitar o provisionamento automático do Microsoft Monitoring Agent:
1. No menu principal da Central de Segurança, selecione **Política de Segurança**.
2. Selecione a assinatura.
3. Em **Política de segurança**, selecione **Coleta de Dados**.
4. Em **Integração**, selecione **Ativar** para habilitar o provisionamento automático.
5. Selecione **Salvar**.

![Habilitar o provisionamento automático][1]

## <a name="default-workspace-configuration"></a>Configuração do espaço de trabalho padrão
Os dados coletados pela Central de Segurança são armazenados no espaço do Log Analytics.  Você pode optar por ter os dados coletados de VMs do Azure armazenados em espaços de trabalho criados pela Central de Segurança ou em um espaço de trabalho existente que você criou.

Para usar o espaço de trabalho existente do Log Analytics:
- O espaço de trabalho deve ser associado à sua assinatura do Azure selecionada.
- No mínimo, você deve ter permissões de leitura para acessar o espaço de trabalho.

Para selecionar um espaço de trabalho existente do Log Analytics:

1. Em **Política de segurança – Coleta de dados**, selecione **Usar outro espaço de trabalho**.

   ![Selecionar um espaço de trabalho existente][2]

2. No menu suspenso, selecione um espaço de trabalho para armazenar os dados coletados.

> [!NOTE]
> No menu suspenso, são mostrados apenas os espaços de trabalho aos quais você tem acesso e que estejam em sua assinatura do Azure.
>
>

3. Selecione **Salvar**.
4. Ao selecionar **Salvar**, você será questionado se deseja reconfigurar as VMs monitoradas.

   - Selecione **Não** se quiser que as novas configurações de espaço de trabalho sejam aplicadas somente às novas VMs. As novas configurações de espaço de trabalho se aplicam somente a novas instalações de agente: VMs recém-descobertas que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se quiser que as novas configurações de espaço de trabalho sejam aplicadas a todas as VMs. Além disso, todas as VMs conectadas a um espaço de trabalho criado da Central de Segurança serão reconectadas ao novo espaço de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, você não deverá excluir os espaços de trabalho criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo espaço de trabalho de destino. Essa operação falhará se um espaço de trabalho for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

   ![Selecionar um espaço de trabalho existente][3]

## <a name="data-collection-tier"></a>Camada de coleta de dados
A Central de Segurança pode reduzir o volume de eventos, mantendo, ao mesmo tempo, eventos suficientes para investigação, auditoria e detecção de ameaças. Você pode escolher a política de filtragem correta para as suas assinaturas e espaços de trabalho dentre quatro conjuntos de eventos a serem coletados pelo agente.

- **Todos os eventos** – para clientes que desejam se certificar de que todos os eventos sejam coletados. Esse é o padrão.
- **Comum** – este é um conjunto de eventos que satisfaz a maioria dos clientes, garantindo uma trilha de auditoria completa.
- **Mínimo** – um conjunto menor de eventos, para clientes que desejam minimizar o volume de eventos.
- **Nenhum** – desabilitar a coleta de eventos de segurança da segurança e dos logs do AppLocker. Para clientes que escolherem esta opção, os painéis de segurança apresentarão somente os logs do Firewall do Windows e as avaliações proativas, como antimalware, linha de base e atualização.

> [!NOTE]
> Esses conjuntos foram projetados para lidar com cenários típicos. Avalie qual atende às suas necessidades antes de implementá-los.
>
>

Para determinar os eventos que farão parte dos conjuntos de eventos **Comum** e **Mínimo**, trabalhamos com clientes e padrões do setor para saber mais sobre a frequência não filtrada de cada evento e seu uso. Neste processo, usamos as seguintes diretrizes:

- **Mínimo** – Certificar-se de que esse conjunto aborde apenas os eventos que possam indicar uma violação com êxito e eventos importantes que tenham um volume muito baixo. Por exemplo, este conjunto conterá logon de usuário bem-sucedidos e malsucedidos (IDs de evento 4624 e 4625), mas não conterá o logout que seja importante para a auditoria, mas não seja significativo para a detecção e que tenha um volume relativamente alto. A maior parte do volume de dados desse conjunto é dos eventos de logon e do evento de criação de processo (ID de evento 4688).
- **Comum** – fornecer uma trilha de auditoria de usuário completa neste conjunto. Por exemplo, este conjunto contém tanto os logons quanto os logoffs de usuário (ID de evento 4634). Incluímos ações de auditoria, como alterações de grupo de segurança, operações Kerberos do controlador de domínio de chave e outros eventos que são recomendados por organizações do setor.

Eventos que têm um volume muito baixo foram incluídos no conjunto Comum, pois a principal motivação para escolhê-los dentre todos os eventos era reduzir o volume e não filtrar eventos específicos.

Aqui está um detalhamento completo das IDs de eventos de Segurança e do AppLocker para cada conjunto:

   ![IDs de eventos][4]

Para escolher a política de filtragem:
1. Na folha **Configurações e política de segurança**, selecione a política de filtragem em **Eventos de Segurança**.
2. Selecione **Salvar**.

   ![Escolher a política de filtragem][5]

## <a name="disable-automatic-provisioning"></a>Desabilitar o provisionamento automático
Você pode desabilitar o provisionamento automático de recursos a qualquer momento, desativando essa configuração na política de segurança. O provisionamento automático é altamente recomendável a fim de obter alertas de segurança e recomendações sobre atualizações do sistema, vulnerabilidades do sistema operacional e proteção do ponto de extremidade.

> [!NOTE]
> Desabilitar o provisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure em que o agente tenha sido provisionado.
>
>

1. Retorne ao menu principal da Central de Segurança e selecione a Política de segurança.

   ![Desabilitar o provisionamento automático][6]

2. Selecione a assinatura em que você deseja desabilitar o provisionamento automático.
3. Na folha **Política de segurança – Coleta de Dados**, em **Integração** selecione **Desativar** para desabilitar o provisionamento automático.
4. Selecione **Salvar**.  

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como a coleta de dados e o provisionamento automático na Central de Segurança funcionam. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) : saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md) – saiba como os dados são gerenciados e protegidos na Central de Segurança do Azure.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png

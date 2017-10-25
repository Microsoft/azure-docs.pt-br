# Visão geral
## [O que é o Active Directory do Azure?](active-directory-whatis.md)
## [Sobre o gerenciamento de identidade do Azure](identity-fundamentals.md)
## [Entender as soluções de identidade do Azure](understand-azure-identity-solutions.md)
## [Escolher uma solução de identidade híbrida](choose-hybrid-identity-solution.md)
## [Gerenciar assinaturas do Azure](active-directory-how-subscriptions-associated-directory.md)
## [Perguntas frequentes](active-directory-faq.md)

# Introdução
## [Introdução ao Microsoft Azure Active Directory](get-started-azure-ad.md)
## [Inscrever-se no Azure AD Premium](active-directory-get-started-premium.md)
## [Como adicionar um nome de domínio personalizado](add-custom-domain.md)
## [Configurar a identidade visual da empresa](customize-branding.md)
## [Adicionar usuários ao Azure AD](add-users-azure-active-directory.md)
## [Atribuir licenças aos usuários](license-users-groups.md)
## [Como configurar a redefinição de senha de autoatendimento](active-directory-passwords-getting-started.md)


# Como
## Planejar e projetar
### [Compreenda a arquitetura do Azure AD](active-directory-architecture.md)
### [Implantar uma Solução de Identidade Híbrida](active-directory-hybrid-identity-design-considerations-overview.md)
### [Mapeamento de declarações no Azure Active Directory](active-directory-claims-mapping.md)
#### Determinar os requisitos
##### [Identidade](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Sincronização de diretórios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Identificar estratégia de ciclo de vida](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planejar segurança de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gerenciamento de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Controle de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planejar o ciclo de vida de identidade
##### [Tarefas](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Estratégia de adoção](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Próximas etapas](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Comparação de ferramentas](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Gerenciar usuários
### [Atribuir licenças usando os grupos](active-directory-licensing-whatis-azure-portal.md)
#### [Atribuir licenças a um grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificar e resolver problemas de licença em um grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrar usuários individuais licenciados para licenciamento baseado em grupo](active-directory-licensing-group-migration-azure-portal.md)
#### [Cenários adicionais para licenciamento baseado em grupos](active-directory-licensing-group-advanced.md)
#### [Exemplos do Azure PowerShell para licenciamento baseado em grupo](active-directory-licensing-ps-examples.md)
#### [Referência de produtos e planos de serviço no Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
### [Redefinir uma senha](active-directory-users-reset-password-azure-portal.md)
### [Gerenciar informações de trabalho do usuário](active-directory-users-work-info-azure-portal.md)
### [Compartilhar contas](active-directory-sharing-accounts.md)



## [Gerenciar grupos e membros](active-directory-manage-groups.md)
### Gerenciar grupos
#### [portal do Azure](active-directory-groups-create-azure-portal.md)
#### [Portal clássico](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell do Azure](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Gerenciar membros do grupo](active-directory-groups-members-azure-portal.md)
### [Gerenciar proprietários do grupo](active-directory-accessmanagement-managing-group-owners.md)
### [Gerenciar associação ao grupo](active-directory-groups-membership-azure-portal.md)
### [Atribuir licenças usando os grupos](active-directory-licensing-whatis-azure-portal.md)
#### [Atribuir licenças a um grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificar e resolver problemas de licença em um grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrar usuários individuais licenciados para licenciamento baseado em grupo](active-directory-licensing-group-migration-azure-portal.md)
#### [Cenários adicionais para licenciamento baseado em grupos](active-directory-licensing-group-advanced.md)
#### [Exemplos do Azure PowerShell para licenciamento baseado em grupo](active-directory-licensing-ps-examples.md)
#### [Referência de produtos e planos de serviço no Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Configurar a expiração de grupos do Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Exibir todos os grupos](active-directory-groups-view-azure-portal.md)
### [Habilitar grupos dedicados](active-directory-accessmanagement-dedicated-groups.md)
### [Adicionar grupo de acesso a aplicativos SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Restaurar um grupo excluído do Office 365](active-directory-groups-restore-azure-portal.md)
### Gerenciar configurações de grupo
#### [Portal do Azure](active-directory-groups-settings-azure-portal.md)
#### [Cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Criar regras avançadas
#### [Portal do Azure](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Portal clássico](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Configurar grupos de autoatendimento](active-directory-accessmanagement-self-service-group-management.md)
### [Solucionar problemas](active-directory-accessmanagement-troubleshooting.md)

## [Gerenciar relatórios](active-directory-reporting-azure-portal.md)
### [Atividade de entrada](active-directory-reporting-activity-sign-ins.md)
### [Atividade de auditoria](active-directory-reporting-activity-audit-logs.md)
### [Usuários em risco](active-directory-reporting-security-user-at-risk.md)
### [Entradas de risco](active-directory-reporting-security-risky-sign-ins.md)
### [Eventos de risco](active-directory-reporting-risk-events.md)
### [Perguntas frequentes](active-directory-reporting-faq.md)
### Tarefas
#### [Configurar localizações nomeadas](active-directory-named-locations.md)
#### [Localizar relatórios de atividades](active-directory-reporting-migration.md)
#### [Usar o Pacote de Conteúdo do Power BI do Active Directory do Azure](active-directory-reporting-power-bi-content-pack-how-to.md)
### Referência
#### [Retenção](active-directory-reporting-retention.md)
#### [Latências](active-directory-reporting-latencies-azure-portal.md)
#### [Notificações](active-directory-reporting-notifications.md)
#### [Códigos de erro da atividade de entrada](active-directory-reporting-activity-sign-ins-errors.md)
#### [Autenticação multifator](active-directory-reporting-activity-sign-ins-mfa.md)
### Solucionar problemas
#### [Dados de auditoria ausentes](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Dados ausentes nos downloads](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Erros de pacote de conteúdo de logs de Atividade do Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
### [Acesso Programático](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Referência de auditoria](active-directory-reporting-api-audit-reference.md)
#### [Referência de entrada](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Pré-requisitos](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Exemplos de auditoria](active-directory-reporting-api-audit-samples.md)
#### [Exemplos de entrada](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Usando certificados](active-directory-reporting-api-with-certificates.md)

## [Gerenciar senhas](active-directory-passwords-overview.md)
### Documentos do usuário
#### [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
#### [Práticas recomendadas de senha](active-directory-secure-passwords.md)
#### [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
### [Licença SSPR](active-directory-passwords-licensing.md)
### [Implantar SSPR](active-directory-passwords-best-practices.md)
### Administradores de TI: redefinir senhas
#### [Portal do Azure](active-directory-users-reset-password-azure-portal.md)
#### [Portal clássico do Azure](active-directory-create-users-reset-password.md)
### [Entender as políticas SSPR](active-directory-passwords-policy.md)
### [Compreender a redefinição de senha](active-directory-passwords-how-it-works.md)
### [Personalizar SSPR](active-directory-passwords-customize.md)
### [Dados usados pelo SSPR](active-directory-passwords-data.md)
### [Relatando no SSPR](active-directory-passwords-reporting.md)
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### [Write-back de senha](active-directory-passwords-writeback.md)
### [Sincronização de hash de senha](./connect/active-directory-aadconnectsync-implement-password-synchronization.md#how-password-synchronization-works)
### [Solucionar problemas](active-directory-passwords-troubleshoot.md)
### [PERGUNTAS FREQUENTES](active-directory-passwords-faq.md)


## Gerenciar dispositivos
### [Introdução](device-management-introduction.md)
### [Usando o portal do Azure](device-management-azure-portal.md)
### [Planejar a Associação do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Perguntas frequentes](device-management-faq.md)
### Tarefas
#### [Configurar dispositivos do Windows 10 registrados pelo Azure AD](device-management-azuread-registered-devices-windows10-setup.md)
#### [Configurar dispositivos adicionados ao Azure AD](device-management-azuread-joined-devices-setup.md)
#### [Configurar dispositivos adicionados ao Azure AD](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Implantar no local](active-directory-device-registration-on-premises-setup.md)
#### [Associação do Azure AD durante a experiência de primeira execução do Windows 10](device-management-azuread-joined-devices-frx.md)
### Solucionar problemas
#### [Dispositivos híbridos do Windows 10 e Windows Server 2016 adicionados ao Azure AD](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Dispositivos híbridos do Windows de herança adicionados ao Azure AD](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Gerenciar aplicativos
### [Visão geral](active-directory-enable-sso-scenario.md)
### [Guia de Introdução](active-directory-integrating-applications-getting-started.md)
### [Tutoriais de integração de aplicativos SaaS](active-directory-saas-tutorial-list.md)
### [Cloud App Discovery](cloudappdiscovery-get-started.md)
#### [Criar relatórios de instantâneo](cloudappdiscovery-set-up-snapshots.md)
#### [Configurar relatório contínuo](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Usar um analisador de log personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
#### Descoberta baseada em agente
##### [O que é o Cloud App Discovery?](active-directory-cloudappdiscovery-whatis.md)
##### [Atualizar configurações do Registro](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
##### [Compreender a segurança e a privacidade](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)


### [Acesse aplicativos remotamente com o Proxy de aplicativo](active-directory-application-proxy-get-started.md)
#### Introdução
##### [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
##### [Publicar aplicativos](application-proxy-publish-azure-portal.md)
##### [Domínios personalizados](active-directory-application-proxy-custom-domains.md)
#### [Logon Único](application-proxy-sso-overview.md)
##### [SSO com KCD](active-directory-application-proxy-sso-using-kcd.md)
##### [SSO com cabeçalhos](application-proxy-ping-access.md)
##### [SSO com compartimentação de senha](application-proxy-sso-azure-portal.md)
#### Conceitos
##### [Conectores](application-proxy-understand-connectors.md)
##### [Segurança](application-proxy-security-considerations.md)
##### [Redes](application-proxy-network-topology-considerations.md)


##### [Atualização do TMG ou UAG](application-proxy-transition-from-uag-tmg.md)

#### Configurações avançadas
##### [Publicar em redes separadas](active-directory-application-proxy-connectors-azure-portal.md)
##### [Servidores proxy](application-proxy-working-with-proxy-servers.md)
##### [Aplicativos com reconhecimento de declarações](active-directory-application-proxy-claims-aware-apps.md)
##### [Aplicativos cliente nativos](active-directory-application-proxy-native-client.md)
##### [Instalação silenciosa](active-directory-application-proxy-silent-installation.md)
##### [Home page personalizada](application-proxy-office365-app-launcher.md)
##### [Converter links embutidos](application-proxy-link-translation.md)
#### Orientações de publicação
##### [Área de Trabalho Remota](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Equipes da Microsoft](application-proxy-teams.md)
#### [Solucionar problemas](active-directory-application-proxy-troubleshoot.md)
#### Usar o portal clássico
##### [Baixar conectores](application-proxy-enable-classic-portal.md)
##### [Publicar aplicativos](active-directory-application-proxy-publish.md)
##### [Usar conectores](active-directory-application-proxy-connectors.md)
##### [Acesso condicional](active-directory-application-proxy-conditional-access.md)

### Gerenciar aplicativos empresariais
#### [Atribuir usuários](active-directory-coreapps-assign-user-azure-portal.md)
#### [Personalizar identidade visual](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Desabilitar entradas de usuário](active-directory-coreapps-disable-app-azure-portal.md)
#### [Remover usuários](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Exibir todos os meus aplicativos](active-directory-coreapps-view-azure-portal.md)
#### [Gerenciar o provisionamento de conta de usuário](active-directory-enterprise-apps-manage-provisioning.md)
#### [Gerenciar logon único para aplicativos corporativos](active-directory-enterprise-apps-manage-sso.md)
#### [Assinatura de certificados avançada para aplicativos SAML](active-directory-enterprise-apps-advance-certificate-options.md)
####[ Ocultar a experiência de um usuário no Azure Active Directory](active-directory-coreapps-hide-third-party-app.md)

### [Gerenciar o acesso aos aplicativos](active-directory-managing-access-to-apps.md)
#### [Acesso de autoatendimento](active-directory-self-service-application-access.md)
#### [Acesso SSO](active-directory-appssoaccess-whatis.md)
#### [Certificados para SSO](active-directory-sso-certs.md)
#### [Restrições de locatário](active-directory-tenant-restrictions.md)
#### [Usar usuários de provisionamento de SCIM](active-directory-scim-provisioning.md)

### [Solucionar problemas](active-directory-application-troubleshoot-content-map.md)
#### [Desenvolvimento de Aplicativos](active-directory-application-dev-troubleshoot-content-map.md)
##### [Configuração e Registro](active-directory-application-dev-config-content-map.md)
##### [Desenvolvimento](active-directory-application-dev-development-content-map.md)
#### [Gerenciamento de Aplicativos](active-directory-application-management-troubleshoot-content-map.md)
##### [Configuração](active-directory-application-config-content-map.md)
##### [Entrar](active-directory-application-sign-in-content-map.md)
##### [Provisionamento](active-directory-application-provisioning-content-map.md)
##### [Gerenciamento do Acesso](active-directory-application-access-content-map.md)
##### [Painel de Acesso](active-directory-application-access-panel-content-map.md)
##### [Proxy de Aplicativo](active-directory-application-proxy-content-map.md)
##### [Acesso condicional](active-directory-application-conditional-access-content-map.md)
### [Desenvolver aplicativos](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Biblioteca de documentos](active-directory-apps-index.md)

## Gerenciar seu diretório
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nomes de domínio personalizados
#### [Visão geral](active-directory-add-domain-concepts.md)
#### Gerenciar nomes de domínio
##### [Portal do Azure](active-directory-domains-manage-azure-portal.md)
##### [Portal clássico](active-directory-add-manage-domain-names.md)
### [Administrar seu diretório](active-directory-administer.md)
### [Vários diretórios](active-directory-licensing-directory-independence.md)
### [Diretórios de O365](active-directory-manage-o365-subscription.md)
### [Inscrição por autoatendimento](active-directory-self-service-signup.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Habilitar](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Configurações de política de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Configurações do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Perguntas frequentes](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Solucionar problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [Integrar parceiros com o Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Admins adicionando usuários B2B](active-directory-b2b-admin-add-users.md)
#### [Trabalhos de informação adicionando usuários B2B](active-directory-b2b-iw-add-users.md)
#### [API e personalização](active-directory-b2b-api.md)
#### [Exemplos de código e do Azure PowerShell](active-directory-b2b-code-samples.md)
#### [Exemplo de portal para iniciar sessão com autoatendimento](active-directory-b2b-self-service-portal.md)
#### [Email de convite](active-directory-b2b-invitation-email.md)
#### [Resgate do convite](active-directory-b2b-redemption-experience.md)
#### [Como adicionar usuários de B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
#### [Acesso condicional para B2B](active-directory-b2b-mfa-instructions.md)
#### [Políticas de compartilhamento B2B](active-directory-b2b-delegate-invitations.md)
#### [Adicionar um usuário B2B a uma função](active-directory-b2b-add-guest-to-role.md)
#### [Grupos dinâmicos e usuários B2B](active-directory-b2b-dynamic-groups.md)
#### [Auditoria e relatórios](active-directory-b2b-auditing-and-reporting.md)
#### [B2B e compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)
#### [Licenciamento](active-directory-b2b-licensing.md)
#### [Limitações atuais](active-directory-b2b-current-limitations.md)
#### [Perguntas frequentes](active-directory-b2b-faq.md)
#### [Solucionando problemas do B2B](active-directory-b2b-troubleshooting.md)
#### [Entender o usuário B2B](active-directory-b2b-user-properties.md)
#### [Token do usuário B2B](active-directory-b2b-user-token.md)
#### [B2B para aplicativos integrados do Azure AD](active-directory-b2b-configure-saas-apps.md)
#### [Mapeamento de declarações do usuário B2B](active-directory-b2b-claims-mapping.md)
#### [Comparar a colaboração B2B com B2C](active-directory-b2b-compare-b2c.md)
#### [Obtendo suporte para B2B](active-directory-b2b-support.md)

### [Integrar identidades locais usando o Azure AD Connect](./connect/active-directory-aadconnect.md)

## [Gerenciar o acesso ao Azure](toc.yml)

## Delegar acesso aos recursos
### [Funções de administrador](active-directory-assign-admin-roles.md)
#### [Atribuir funções de administrador](active-directory-users-assign-role-azure-portal.md)
### [Unidades administrativas](active-directory-administrative-units-management.md)
### [Configurar tempo de vida de tokens](active-directory-configurable-token-lifetimes.md)

## Análises de acesso
### [Visão geral de revisões de acesso](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md)
### [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md)
### [Como executar uma análise de acesso](active-directory-azure-ad-controls-perform-access-review.md)
### [Como analisar seu acesso](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Acesso de convidado com análises de acesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Gerenciando o acesso de usuário com análises](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Gerenciamento de programas e controles](active-directory-azure-ad-controls-manage-programs-controls.md)


## Proteger suas identidades
### [Acesso condicional](active-directory-conditional-access-azure-portal.md)
#### [Controles](active-directory-conditional-access-controls.md)
#### [Introdução](active-directory-conditional-access-azure-portal-get-started.md)
#### [práticas recomendadas](active-directory-conditional-access-best-practices.md)
#### [Entender as políticas do dispositivo para os serviços do Office 365](active-directory-conditional-access-device-policies.md)
#### Tarefas
##### [Configurar o acesso condicional com base no dispositivo](active-directory-conditional-access-policy-connected-applications.md)
##### [Configurar o acesso condicional com base no aplicativo](active-directory-conditional-access-mam.md)
##### [Forneça os termos de uso para usuários e aplicativos](active-directory-tou.md)
##### [Configurar a conectividade VPN](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [Configurar o SharePoint e o Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Correção](active-directory-conditional-access-device-remediation.md)
#### [Referência técnica](active-directory-conditional-access-technical-reference.md)
#### [Perguntas frequentes](active-directory-conditional-faqs.md)
#### [Portal clássico](active-directory-conditional-access.md)
##### [Introdução](active-directory-conditional-access-azuread-connected-apps.md)

### Windows Hello
#### [Autenticar sem senhas](active-directory-azureadjoin-passport.md)
#### [Habilitar o Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md)
### Autenticação baseada em certificado
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Introdução](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Habilitar](active-directory-identityprotection-enable.md)
#### [Detectar vulnerabilidades](active-directory-identityprotection-vulnerabilities.md)
#### [Eventos de risco](active-directory-identity-protection-risk-events.md)
#### [Notificações](active-directory-identityprotection-notifications.md)
#### [Experiência de conexão](active-directory-identityprotection-flows.md)
#### [Simular eventos de risco](active-directory-identityprotection-playbook.md)
#### [Desbloquear usuários](active-directory-identityprotection-unblock-howto.md)
#### [Perguntas frequentes](active-directory-identity-protection-faqs.md)
#### [Glossário](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Integrar outros serviços com o Azure AD]()
### [Habilitar a integração do LinkedIn](linkedin-integration.md)

## [Implante o AD DS em máquinas virtuais do Azure](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory nas VMs do Azure](active-directory-deploying-ws-ad-guidelines.md)
### [Controlador de domínio de réplica em uma rede virtual do Azure](active-directory-install-replica-active-directory-domain-controller.md)
### [Nova floresta em uma rede virtual do Azure](active-directory-new-forest-virtual-machine.md)



## [Implantar o AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
### [Alta disponibilidade](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Alterar o algoritmo de hash da assinatura](active-directory-federation-sha256-guidance.md)

## [Solucionar problemas](active-directory-troubleshooting-support-howto.md)
### [O item de solução de problemas do Active Directory sumiu ou não está disponível](active-directory-troubleshooting.md)

## Implantar a Prova de conceito (PoC) do Azure AD
### [Manual de PoC: introdução](active-directory-playbook-intro.md)
### [Manual de PoC: ingredientes](active-directory-playbook-ingredients.md)
### [Manual de PoC: implementação](active-directory-playbook-implementation.md)
### [Manual de PoC: blocos de construção](active-directory-playbook-building-blocks.md)


# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [Cmdlets do Azure PowerShell](/powershell/azure/overview)
## [Referência de API Java](/java/api)
## [API do .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Restrições e limites de serviço](active-directory-service-limits-restrictions.md)

# Relacionados
## [Autenticação Multifator](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD para desenvolvedores](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Recursos
## [Fórum de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Preços](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)

---
title: Provedores de autenticação multifator do Azure-Azure Active Directory
description: Quando você deve usar um Provedor de Autenticação com o Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf50a8f58978a010fe3d8228ace8579fcf52eb38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81309893"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Quando usar um Provedor de Autenticação Multifator do Microsoft Azure

> [!IMPORTANT]
> A partir de 1º de setembro de 2018, novos provedores de autenticação não poderão mais ser criados. Os provedores de autenticação existentes podem continuar sendo usados e atualizados, mas a migração não é mais possível. A autenticação multifator continuará disponível como um recurso nas licenças do Microsoft Azure Active Directory Premium.

A autenticação em duas etapas está disponível por padrão para os administradores globais que têm o Azure Active Directory e os usuários do Office 365. No entanto, se você quiser aproveitar os [recursos avançados](howto-mfa-mfasettings.md), então, deverá adquirir a versão completa da Autenticação Multifator do Azure (MFA).

Um Provedor de Autenticação Multifator do Azure é usado para aproveitar os recursos fornecidos pela Autenticação Multifator do Microsoft Azure para usuários que **não têm licenças**.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Limitações relacionadas ao SDK do Azure MFA

Observe que o SDK foi descontinuado e continuará funcionando até 14 de novembro de 2018. Após essa data, as chamadas para o SDK falharão.

## <a name="what-is-an-mfa-provider"></a>O que é um Provedor MFA?

Há dois tipos de provedores de autenticação e a diferença está em como a assinatura do Azure é cobrada. A opção por autenticação calcula o número de autenticações executadas em seu locatário em um mês. Essa será a melhor opção se você tiver um número de usuários que se autenticam apenas ocasionalmente. A opção por usuário calcula o número de pessoas no seu locatário que executam a verificação em duas etapas em um mês. Essa é a melhor opção se você tiver alguns usuários com licenças, mas precisar estender MFA para mais usuários, além de seus limites de licença.

## <a name="manage-your-mfa-provider"></a>Gerenciar seu `Provedor MFA

Não é possível alterar o modelo de uso (por usuário habilitado ou por autenticação) após a criação de um provedor de MFA.

Se você comprou licenças suficientes para cobrir todos os usuários que estão habilitados para MFA, é possível excluir o provedor MFA completamente.

Se o seu provedor de MFA não estiver vinculado a um locatário do Azure AD, ou você vincula o novo provedor de MFA a um locatário diferente do Azure AD, as opções de definições de usuário e de configuração não serão transferidas. Além disso, os servidores MFA existentes do Azure precisarão ser reativados usando as credenciais de ativação geradas por meio do provedor de MFA.

### <a name="removing-an-authentication-provider"></a>Removendo um provedor de autenticação

> [!CAUTION]
> Não há nenhuma confirmação ao excluir um provedor de autenticação. Selecionar **excluir** é um processo permanente.

Os provedores de autenticação podem ser encontrados nos provedores de MFA de segurança do **portal do Azure**  >  **Azure Active Directory**  >  **Security**  >  **MFA**  >  **Providers**. Clique em provedores listados para ver detalhes e configurações associadas a esse provedor.

Antes de remover um provedor de autenticação, anote todas as configurações personalizadas definidas em seu provedor. Decida quais configurações precisam ser migradas para as configurações gerais do MFA de seu provedor e conclua a migração dessas configurações. 

Os servidores do Azure MFA vinculados aos provedores precisarão ser reativados usando as **Azure portal**credenciais geradas em  >  **Azure Active Directory**  >  **Security**  >  **MFA**  >  **configurações do servidor**portal do Azure Azure Active Directory Security MFA. Antes de reativar, os seguintes arquivos devem ser excluídos do `\Program Files\Multi-Factor Authentication Server\Data\` diretório nos servidores do Azure MFA em seu ambiente:

- cacerts
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Excluir um provedor de autenticação da portal do Azure](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Depois de confirmar que todas as configurações foram migradas, você pode navegar até o **portal do Azure**  >  **Azure Active Directory**  >  provedores de MFA de**segurança**  >  **MFA**  >  **Providers** e selecionar as reticências **...** e selecionar **excluir**.

> [!WARNING]
> A exclusão de um provedor de autenticação excluirá todas as informações de relatório associadas a esse provedor. Talvez você queira salvar os relatórios de atividade antes de excluir seu provedor.

> [!NOTE]
> Os usuários com versões mais antigas do aplicativo Microsoft Authenticator e do servidor MFA do Azure podem precisar registrar novamente seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

[Definir as configurações da Autenticação Multifator](howto-mfa-mfasettings.md)

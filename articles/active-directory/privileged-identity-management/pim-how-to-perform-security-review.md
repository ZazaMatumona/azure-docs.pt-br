---
title: Examinar o acesso às funções do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como examinar o acesso de funções de Azure Active Directory no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/24/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4096c65fa4375152f44891d4a0a5aac4b9077cf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743840"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Examinar o acesso às funções do Azure AD no Privileged Identity Management

O Privileged Identity Management (PIM) simplifica o modo como as empresas gerenciam o acesso privilegiado a recursos no Azure Active Directory (AD) e outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune. Siga as etapas neste artigo para revisar com êxito as funções atribuídas.

Se você for atribuído a uma função administrativa, o administrador de função com privilégios de sua organização poderá solicitar que você confirme regularmente que ainda precisa da função para seu trabalho. Você pode receber um email que inclui um link ou pode ir diretamente para a [portal do Azure](https://portal.azure.com) e começar.

Se você for um administrador com privilégios de função ou um administrador global interessado em revisões de acesso, obtenha mais detalhes em [Como iniciar uma revisão de acesso](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Adicionar um bloco do painel PIM

Se você não tiver o serviço Privileged Identity Management fixado ao seu painel no seu portal do Azure, siga estas etapas para começar.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione seu nome de usuário no canto superior direito do portal do Azure e selecione a organização do Azure AD na qual você estará operando.
3. Selecione **Todos os serviços** e use a caixa de texto Filtrar para pesquisar o **Azure AD Privileged Identity Management**.
4. Marque **Fixar no painel** e então clique em **Criar**. O aplicativo Privileged Identity Management será aberto.

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso

Ao aprovar ou negar o acesso, você está apenas dizendo ao revisor se ainda usa essa função ou não. Escolha **Aprovar** se você quiser manter a função ou **Negar** se não precisar mais do acesso. Seu status não mudará imediatamente até que o revisor aplique os resultados.
Siga estas etapas para localizar e concluir a análise de acesso:

1. No serviço Privileged Identity Management, selecione **revisar acesso privilegiado**. Se você tiver quaisquer revisões de acesso pendentes, elas aparecerão na página de **revisões de acesso** do Azure AD.
2. Selecione a análise que deseja concluir.
3. A menos que tenha criado a análise, você aparece como o único usuário na análise. Selecione a marca de seleção ao lado de seu nome.
4. Escolha **Aprovar** ou **Negar**. Talvez seja necessário incluir um motivo para a sua decisão na caixa de texto **Fornecer um motivo** .  
5. Feche a folha **Funções de análise do AD do Azure** .

## <a name="next-steps"></a>Próximas etapas

- [Realizar uma revisão de acesso das minhas funções de recurso do Azure no PIM](pim-resource-roles-perform-access-review.md)

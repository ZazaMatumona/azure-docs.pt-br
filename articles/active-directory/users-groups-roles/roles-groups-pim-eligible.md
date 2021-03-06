---
title: Atribuir uma função a um grupo usando Privileged Identity Management no Azure AD | Microsoft Docs
description: Visualize funções personalizadas do Azure AD para delegar o gerenciamento de identidades. Gerencie funções do Azure no portal do Azure, no PowerShell ou na API do Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343da87048cf43c04a137376e9a7f24270ce729a
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475949"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Atribuir uma função a um grupo usando Privileged Identity Management

Este artigo descreve como você pode atribuir uma função de Azure Active Directory (Azure AD) a um grupo usando Azure AD Privileged Identity Management (PIM).

> [!NOTE]
> Você deve usar a versão atualizada do Privileged Identity Management para poder atribuir um grupo a uma função do Azure AD usando o PIM. Você pode estar em uma versão mais antiga do PIM se sua organização do Azure AD aproveitar a API Privileged Identity Management. Nesse caso, entre em contato com o alias pim_preview@microsoft.com para mover sua organização e atualizar sua API. Saiba mais em [funções e recursos do Azure AD no PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Usando o centro de administração do Azure AD

1. Entre no [Azure ad Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) como um administrador de função com privilégios ou administrador global em sua organização.

1. Selecione **Privileged Identity Management**  >  funções de**funções do Azure ad**  >  **Roles**  >  **Adicionar atribuições**

1. Selecione uma função e, em seguida, selecione um grupo. Somente os grupos qualificados para atribuição de função (grupos de função atribuíveis) são exibidos e não todos os grupos.

    ![selecionar o usuário a quem você está atribuindo a função](./media/roles-groups-pim-eligible/select-member.png)

1. Selecione a configuração de associação desejada. Para funções que exigem ativação, escolha **qualificado**. Por padrão, o usuário estaria permanentemente qualificado, mas você também pode definir uma hora de início e de término para a elegibilidade do usuário. Quando estiver concluído, pressione salvar e adicionar para concluir a atribuição de função.

    ![selecionar o usuário a quem você está atribuindo a função](./media/roles-groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>Usando o PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>Baixar o módulo PowerShell de visualização do Azure AD

Para instalar o módulo de #PowerShell do Azure AD, use os seguintes cmdlets:

```powershell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto para uso, use o seguinte cmdlet:

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Atribuir um grupo como um membro qualificado de uma função

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Usando a API Microsoft Graph

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>Próximas etapas

- [Usar grupos de nuvem para gerenciar atribuições de função](roles-groups-concept.md)
- [Solucionando problemas de funções atribuídas a grupos de nuvem](roles-groups-faq-troubleshooting.md)
- [Definir as configurações de função de administrador do Azure AD no Privileged Identity Management](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso do Azure no Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md)

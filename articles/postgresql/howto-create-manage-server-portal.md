---
title: Gerenciar o banco de dados do Azure para PostgreSQL-portal do Azure
description: Saiba como gerenciar um banco de dados do Azure para o servidor PostgreSQL do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 11/20/2019
ms.openlocfilehash: 908a61a00f0e33016074a6f985271ac94157fdf4
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854990"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Gerenciar um servidor de banco de dados do Azure para PostgreSQL usando o portal do Azure

Este artigo mostra como gerenciar seu banco de dados do Azure para servidores PostgreSQL. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="sign-in"></a>Entrar

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor

Visite o guia de [início rápido](quickstart-create-server-database-portal.md) para saber como criar e começar a usar um banco de dados do Azure para o servidor PostgreSQL.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Após a criação do servidor, você pode dimensionar entre as camadas de Uso Geral e com otimização de memória à medida que suas necessidades mudam. Você também pode dimensionar a computação e a memória aumentando ou diminuindo o vCores. O armazenamento pode ser dimensionado verticalmente (no entanto, você não pode dimensionar o armazenamento verticalmente).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Dimensionar entre as camadas de Uso Geral e com otimização de memória

Você pode dimensionar de Uso Geral para a memória otimizada e vice-versa. Não há suporte para a alteração de e para a camada básica após a criação do servidor.

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Selecione **uso geral** ou **memória otimizada**, dependendo do que você está dimensionando.

   ![Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para PostgreSQL](./media/howto-create-manage-server-portal/change-pricing-tier.png)

   > [!NOTE]
   > A alteração de camadas causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.

### <a name="scale-vcores-up-or-down"></a>Dimensionar vCores para cima ou para baixo

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração **vCore**, movendo o controle deslizante para o valor desejado.

   ![Captura de tela de portal do Azure para escolher a opção vCore no banco de dados do Azure para PostgreSQL](./media/howto-create-manage-server-portal/scaling-compute.png)

   > [!NOTE]
   > O dimensionamento de vCores causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.

### <a name="scale-storage-up"></a>Escalar o armazenamento verticalmente

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração de **armazenamento** movendo o controle deslizante para cima até o valor desejado.

   ![Captura de tela de portal do Azure para escolher a escala de armazenamento no banco de dados do Azure para PostgreSQL](./media/howto-create-manage-server-portal/scaling-storage.png)

   > [!NOTE]
   > O armazenamento não pode ser reduzido verticalmente.

3. Selecione **OK** para salvar as alterações.

## <a name="update-admin-password"></a>Atualizar senha do administrador

Você pode alterar a senha da função de administrador usando o portal do Azure.

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **Redefinir senha**.

   ![Captura de tela de portal do Azure para redefinir a senha no banco de dados do Azure para PostgreSQL](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Insira uma nova senha e confirme-a. A caixa de texto solicitará os requisitos de complexidade de senha.

   ![Captura de tela de portal do Azure para redefinir sua senha e salvar no banco de dados do Azure para PostgreSQL](./media/howto-create-manage-server-portal/reset-password.png)

3. Selecione **OK** para salvar a nova senha.

## <a name="delete-a-server"></a>Excluir um servidor

Você pode excluir o servidor se não precisar mais dele. 

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **excluir**.

   ![Captura de tela de portal do Azure para excluir o servidor no banco de dados do Azure para PostgreSQL](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digite o nome do servidor na caixa de entrada para confirmar que este é o servidor que você deseja excluir.

   ![Captura de tela de portal do Azure para confirmar a exclusão do servidor no banco de dados do Azure para PostgreSQL](./media/howto-create-manage-server-portal/confirm-delete.png)

   > [!NOTE]
   > A exclusão de um servidor é irreversível.

3. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [backups e restauração do servidor](howto-restore-server-portal.md)
- Saiba mais sobre [as opções de ajuste e monitoramento no banco de dados do Azure para PostgreSQL](concepts-monitoring.md)

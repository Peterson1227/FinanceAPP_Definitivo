# FinanceAPP_V2 — V7 | Correções de data e notificações

Base: **V6 corrigida (HomeScreen.kt com setValue)**. A V7 mantém o layout, as telas, o banco Room e os dados da V6.

## O que mudou

1. No cartão de parcelas/receitas recorrentes, a data exibida deixa de ser sempre a data da primeira dívida: quando existe pagamento, o cartão mostra a data da última confirmação de pagamento/recebimento; antes do primeiro pagamento, mostra o vencimento programado.
2. Na lista expandida, cada parcela/mês mostra a competência **MM/AAAA**, o vencimento se pendente e o dia real da confirmação se pago/recebido. O lançamento gerado no extrato usa a mesma data da confirmação armazenada em `finance_plan_payments`.
3. A recuperação de lembretes mensais deixa de marcar um aviso como perdido apenas porque o horário acabou de passar. A situação exibida no histórico não presume mais que o celular estava desligado.
4. Mantido o agendamento principal por `AlarmManager` (`setExactAndAllowWhileIdle` autorizado, `setAndAllowWhileIdle` caso contrário), com reserva **aproximada** por `WorkManager`, cerca de dois minutos depois, quando o alarme principal não tiver sido entregue. A tarefa é cancelada junto com o lembrete. Uma notificação já entregue não é duplicada pela reserva.
5. `versionCode` 4 e `versionName` 1.3. Identificador do app preservado para atualização por cima.

## Atualizar sem perder os dados

- Faça uma cópia da pasta atual do projeto e **não desinstale** o app do celular.
- Se usar o ZIP completo, extraia-o em um novo local e abra a pasta `FinanceAPP_V2` pelo Android Studio. Se usar o ZIP de alterações, extraia na **pasta PAI** que contém `FinanceAPP_V2`, substituindo somente os arquivos da lista.
- Faça `Sync Project with Gradle Files` se solicitado e use **Build → Generate App Bundles or APKs → Generate APKs**.
- Instale o APK por cima do anterior usando o mesmo `applicationId` e a mesma assinatura de desenvolvimento. Se houver erro de assinatura, não desinstale sem antes guardar os dados.

## Testes prioritários no celular

- Crie uma despesa de 3 parcelas. Confirme a primeira como paga. O cartão deve mostrar **Pago em [data de hoje]** e a lista aberta deve mostrar **Parcela 1/3 · MM/AAAA** e **Pago em [data de hoje]**. Confirme também o extrato.
- Cadastre uma receita recorrente, marque um mês como recebido e confira **Recebido em** com a data efetiva, separada da competência do mês.
- Agende **dois lembretes novos**, um único e outro mensal, para daqui a pelo menos 5 minutos. Ative notificações e, no Android 12+, a permissão opcional **Alarmes e lembretes** na engrenagem. Saia usando a tela de recentes, bloqueie a tela, **não use Forçar parada**. Observe se a notificação chega e, se possível, a diferença entre o horário solicitado e o recebido.
- Para o lembrete mensal, confirme que aparece a próxima ocorrência futura e apenas uma notificação do aviso já disparado.
- Teste ainda exclusão e cancelamento, para confirmar que nenhum aviso cancelado é enviado.

## Limitações e notas de diagnóstico

- **Não foi possível compilar o APK ou reproduzir o teste de aparelho neste ambiente.** A V7 foi verificada estaticamente e os cálculos de competência/parcelas foram testados isoladamente com Kotlin/JVM.
- `WorkManager` é uma reserva de entrega sujeita a economia de bateria e pode atrasar. Nenhuma implementação comum garante notificação após **Forçar parada**. O sistema/fabricante pode impor restrições mesmo ao remover o app dos recentes.
- Se um novo lembrete não for enviado, envie as linhas do **Logcat** filtrando por `FinanceReminder` e a tela de **Lembretes → Histórico**. Isso permitirá localizar falha no agendamento ou ausência de autorização.
- O histórico `Não disparou no horário` da versão antiga continua registrado; a V7 não altera retroativamente o estado dos lembretes anteriores.

## Arquivos alterados

- `app/src/main/java/com/financeapp/nativeapp/presentation/home/PlannedTransactionCard.kt`
- `app/src/main/java/com/financeapp/nativeapp/planning/FinancialPlanRepository.kt`
- `app/src/main/java/com/financeapp/nativeapp/reminders/ReminderScheduler.kt`
- `app/src/main/java/com/financeapp/nativeapp/reminders/ReminderReceiver.kt`
- `app/src/main/java/com/financeapp/nativeapp/reminders/ReminderFallbackWorker.kt` (novo)
- `app/src/main/java/com/financeapp/nativeapp/presentation/reminders/ReminderScreen.kt`
- `app/build.gradle.kts`

Não foram alterados: `AndroidManifest.xml`, `Room`, Firebase, biometria e telas de cadastro/navegação.

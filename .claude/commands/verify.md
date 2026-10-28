---
descrição: Execute as verificações que o CI realiza nas solicitações de pull e relate o que falhou.
---

Execute as verificações que o arquivo `.github/workflows/pr-checks.yml` executa, nesta ordem, a partir da raiz do repositório. Use `--continue` para que uma falha não oculte a próxima.

1. `./gradlew spotlessApply` formata os arquivos alterados em relação a `origin/main`. Se reportar um erro de lint que não consegue corrigir (regras do Compose, como um `modificador` ausente ou ordem de parâmetros), corrija o código manualmente.
2. `./gradlew compileKotlinJvm compileAndroidMain :androidApp:compileDebugKotlin jvmTest detekt :konsist-test:test --continue`
3. `./gradlew lintDebug lintAndroidMain --continue`
4. `./gradlew stabilityCheck` em sua própria invocação, após a compilação da JVM e do Android na etapa 2.

O CI também executa uma tarefa `Agent config` (agnix); não há nenhuma etapa local para isso.

Em seguida, registre cada etapa como concluída com sucesso ou reprovada, incluindo as primeiras linhas de cada falha, o arquivo e a linha correspondentes.

- Corrija o que essa alteração causou. Se a falha for proveniente de um código que não foi alterado, indique isso em vez de corrigi-la.
- Não atualize uma linha de base (`detektBaseline`, `updateLintBaseline` ou `updateLintBaselineAndroidMain`, `stabilityDump`) a menos que a alteração tenha como objetivo modificá-la, e informe isso quando o fizer.
- Se o Gradle não conseguir iniciar a partir do seu shell, execute os mesmos comandos em um terminal e leia a saída de lá.

Detalhes: `docs/agents/checks.md`.

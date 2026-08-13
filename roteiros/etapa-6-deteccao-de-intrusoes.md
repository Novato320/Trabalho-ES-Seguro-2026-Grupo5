# Etapa 6 — Monitoramento e Detecção de Intrusões

## Introdução

*(preencher — responsável: [seu nome])*

- O que é detecção de intrusões
- Diferença entre prevenção e detecção
- Quais eventos do sistema deveriam ser registrados

---

## Regra 1 de detecção

*(preencher — responsável: Ortiz)*

| Campo | Descrição |
|---|---|
| Risco observado | |
| Fonte de dados | |
| Condição de alerta | |
| Resposta inicial | |

---

## Regra 2 de detecção

*(preencher — responsável: Jaques)*

| Campo | Descrição |
|---|---|
| Risco observado | |
| Fonte de dados | |
| Condição de alerta | |
| Resposta inicial | |

---

## Regra 3 de detecção

A Regra 3 está relacionada ao risco **R06 — Obtenção indevida de privilégios administrativos**, identificado na Etapa 2 como um risco de nível **alto**, decorrente de falhas no controle de autorização e na validação de papéis dos usuários (clientes, estabelecimentos e entregadores) no backend do sistema.

| Campo | Descrição |
|---|---|
| **Risco observado** | Um estabelecimento, entregador ou cliente consegue acessar funções administrativas do sistema sem possuir a permissão correspondente, explorando falhas de autorização ou validação insuficiente de papéis no backend (R06). |
| **Fonte de dados** | Logs de autorização e de acesso às rotas administrativas (endpoints de gerenciamento de usuários, pedidos, estabelecimentos e permissões), incluindo o perfil do usuário autenticado, a rota acessada e o resultado da tentativa (permitida ou negada). |
| **Condição de alerta** | Um usuário com perfil não administrativo (cliente, estabelecimento ou entregador) realiza uma ou mais tentativas de acesso a rotas ou funções administrativas, ou uma conta passa a executar ações fora do padrão histórico de seu perfil (por exemplo, um estabelecimento tentando alterar permissões de outros usuários). |
| **Resposta inicial** | Bloquear imediatamente a operação no backend (independente da resposta da interface), registrar o evento com destaque de severidade alta, alertar a equipe de segurança e revisar as permissões e o histórico recente de ações da conta envolvida antes de restabelecer seu uso normal. |

---

## O que acontece depois de um alerta

Depois que uma das regras de detecção gera um alerta, o roteiro de resposta inicial deverá seguir, em linhas gerais, as seguintes etapas:

1. **Registro do alerta.** O evento é registrado com data, hora, conta envolvida, regra que disparou o alerta e dados de contexto (IP, dispositivo, rota acessada), garantindo rastreabilidade para investigação posterior.

2. **Triagem inicial.** A equipe responsável avalia rapidamente se o alerta é um verdadeiro positivo (comportamento realmente suspeito), um falso positivo (comportamento legítimo que disparou a regra por engano) ou um evento que precisa de mais dados para ser classificado.

3. **Contenção proporcional ao risco.** Para alertas de risco alto ou crítico — como os relacionados a R01, R04, R05 e R06 — pode ser aplicada uma contenção imediata, como bloqueio temporário da conta, exigência de nova autenticação ou limitação de requisições, evitando prejuízo maior enquanto a investigação ocorre.

4. **Investigação e análise.** A equipe analisa os logs relacionados ao evento, verifica se houve outras tentativas semelhantes, identifica o alcance do problema (quantos usuários ou recursos foram afetados) e determina se o alerta está de fato associado a uma tentativa de intrusão.

5. **Comunicação.** Caso o incidente seja confirmado, os responsáveis pelo sistema e, se necessário, os usuários afetados são comunicados, seguindo os critérios de gravidade definidos pela equipe.

6. **Correção e ajuste dos controles.** Após a análise, são aplicadas correções (por exemplo, revisão de permissões, correção de falha de autorização) e, se necessário, as regras de detecção são ajustadas para reduzir falsos positivos ou cobrir novos comportamentos identificados.

7. **Encerramento e registro do aprendizado.** O incidente (ou o falso positivo) é documentado, incluindo a causa, a resposta aplicada e as lições aprendidas, para alimentar a melhoria contínua do monitoramento nas próximas etapas do projeto (Etapa 7 — DevSecOps).
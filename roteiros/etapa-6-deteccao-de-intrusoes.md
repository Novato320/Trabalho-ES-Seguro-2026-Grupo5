# Etapa 6 — Monitoramento e Detecção de Intrusões

## Introdução

A detecção de intrusões consiste no monitoramento de eventos e comportamentos
do sistema com o objetivo de identificar atividades suspeitas, tentativas de
ataque, uso indevido de contas ou ações que possam indicar um incidente de
segurança.

A prevenção e a detecção possuem funções diferentes, mas complementares.
A prevenção utiliza controles destinados a impedir ou reduzir a probabilidade
de um incidente, como autenticação multifator, controle de autorização,
validação de dados no backend e limitação de requisições. A detecção, por
outro lado, busca identificar comportamentos suspeitos que estejam ocorrendo
ou que já tenham ocorrido, permitindo que a equipe responsável inicie uma
resposta.

Por exemplo, limitar repetidas tentativas de autenticação é uma medida
preventiva. Registrar essas tentativas e gerar um alerta ao identificar um
comportamento anormal é uma medida de detecção.

Para permitir esse monitoramento, o sistema de delivery deveria registrar
eventos relevantes de segurança, como:

- tentativas de autenticação bem-sucedidas e malsucedidas;
- bloqueios temporários de contas;
- falhas em verificações adicionais de autenticação;
- acessos negados por falha de autorização;
- tentativas de acesso a recursos pertencentes a outros usuários;
- alterações e cancelamentos de pedidos;
- alterações no status das entregas;
- operações realizadas por contas administrativas;
- alterações de permissões ou papéis de usuários;
- grande quantidade de requisições em um curto período;
- erros relevantes da aplicação e do banco de dados;
- eventos relacionados a pagamentos.

Sempre que possível, esses registros devem conter informações suficientes
para investigação, como data e horário, usuário envolvido, origem da
requisição, operação realizada, recurso acessado e resultado da ação.

Os logs também devem evitar o armazenamento desnecessário de informações
sensíveis, como senhas, tokens de autenticação completos ou dados de
pagamento.

---

## Regra 1 de detecção

A Regra 1 está relacionada ao risco **R01 — Uso indevido de conta**, identificado
nas etapas anteriores do projeto. O objetivo é detectar um possível acesso
não autorizado por meio da observação de repetidas falhas de autenticação.

| Campo | Descrição |
|---|---|
| **Risco observado** | R01 — Uso indevido de conta. Um atacante pode realizar repetidas tentativas de autenticação buscando obter acesso indevido à conta de um usuário. |
| **Fonte de dados** | Logs de autenticação, contendo informações sobre tentativas bem-sucedidas e malsucedidas, conta envolvida, horário e origem da requisição. |
| **Condição de alerta** | Múltiplas tentativas de autenticação malsucedidas para a mesma conta em um curto intervalo de tempo. |
| **Resposta inicial** | Gerar um alerta para a equipe responsável, limitar temporariamente novas tentativas de autenticação e analisar os registros relacionados à conta e à origem das requisições. |

Uma falha isolada de autenticação pode ocorrer por um erro legítimo do
usuário. Entretanto, várias tentativas malsucedidas para a mesma conta em
um curto período podem indicar uma tentativa de descoberta de credenciais
ou de acesso não autorizado.

A partir dos logs de autenticação, o sistema poderia identificar esse padrão
e gerar um alerta. A equipe responsável poderia então analisar informações
como horário das tentativas, conta envolvida e origem das requisições para
determinar se o comportamento representa uma atividade suspeita.

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

# Tratamento dos riscos com o NIST CSF

Após a identificação e a priorização dos riscos, foram definidas estratégias e medidas para reduzir sua probabilidade ou seu impacto.

O NIST Cybersecurity Framework 2.0 foi utilizado para organizar os resultados de segurança esperados. O framework possui seis funções:

| Função | Finalidade no projeto |
|---|---|
| Govern | Definir políticas, responsabilidades, prioridades e critérios de aceitação de riscos |
| Identify | Conhecer os ativos, dependências, vulnerabilidades e riscos do sistema |
| Protect | Implementar salvaguardas para reduzir a probabilidade ou o impacto dos incidentes |
| Detect | Identificar eventos suspeitos, falhas e possíveis incidentes |
| Respond | Conter, analisar, comunicar e tratar incidentes detectados |
| Recover | Restaurar serviços e dados e reduzir os prejuízos causados |

As funções organizam os resultados esperados, mas não determinam uma tecnologia específica. Os controles apresentados a seguir são propostas do grupo para o sistema de delivery.

## Estratégias de tratamento

Foram consideradas quatro estratégias:

| Estratégia | Descrição |
|---|---|
| Evitar | Eliminar a atividade ou condição que dá origem ao risco |
| Reduzir | Implementar medidas para diminuir a probabilidade ou o impacto |
| Compartilhar | Atribuir parte da operação ou das consequências a um terceiro |
| Aceitar | Reconhecer e manter conscientemente o risco, com justificativa e acompanhamento |

A aceitação de um risco não significa ignorá-lo. A decisão deve ser justificada, aprovada por uma pessoa responsável e revisada quando o contexto mudar.

## Estratégia escolhida para cada risco

| Risco                                                  | Nível inicial | Estratégia principal         | Justificativa                                                                                                                |
| ------------------------------------------------------ | ------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| R01 — Uso indevido de conta                            | Crítico       | Reduzir                      | O acesso ao sistema é necessário, mas pode receber proteções adicionais de autenticação, reautenticação e monitoramento      |
| R02 — Alteração indevida de dados do pedido            | Alto          | Reduzir                      | As operações de pedido precisam continuar disponíveis, mas devem possuir validação e controles de integridade no backend     |
| R03 — Negação de uma operação de entrega               | Médio         | Reduzir e aceitar o residual | Registros confiáveis e confirmação da entrega reduzem o risco, mas não eliminam completamente a possibilidade de contestação |
| R04 — Acesso indevido a dados de outros usuários       | Crítico       | Reduzir                      | Os dados são necessários ao funcionamento do sistema, mas seu acesso deve ser limitado, autorizado e monitorado              |
| R05 — Indisponibilidade do sistema                     | Crítico       | Reduzir e compartilhar       | O sistema pode implementar proteções próprias e utilizar serviços especializados para absorção de picos e tráfego malicioso  |
| R06 — Obtenção indevida de privilégios administrativos | Alto          | Reduzir                      | As funções administrativas são necessárias, mas devem possuir controle rigoroso de autorização e menor privilégio            |

## Mapeamento dos riscos para as funções do NIST CSF

| Risco                                                  | Govern | Identify | Protect | Detect | Respond | Recover |
| ------------------------------------------------------ | :----: | :------: | :-----: | :----: | :-----: | :-----: |
| R01 — Uso indevido de conta                            |   X    |          |    X    |   X    |    X    |    X    |
| R02 — Alteração indevida de dados do pedido            |        |          |    X    |   X    |    X    |    X    |
| R03 — Negação de uma operação de entrega               |   X    |          |    X    |   X    |    X    |         |
| R04 — Acesso indevido a dados de outros usuários       |   X    |          |    X    |   X    |    X    |    X    |
| R05 — Indisponibilidade do sistema                     |   X    |    X     |    X    |   X    |    X    |    X    |
| R06 — Obtenção indevida de privilégios administrativos |   X    |          |    X    |   X    |         |         |

O preenchimento da tabela indica quais funções precisam ser consideradas no tratamento de cada risco. As marcações foram definidas de acordo com os controles e resultados esperados para cada situação, evitando associar automaticamente todas as funções a todos os riscos.

Protect e Detect aparecem com maior frequência porque os controles propostos priorizam prevenção, validação, monitoramento e rastreabilidade. Govern foi associado aos riscos que dependem de políticas, responsabilidades ou revisão de permissões. Identify foi mantido quando o conhecimento de capacidade, dependências e recursos do sistema é especialmente relevante, como no risco de indisponibilidade. Respond e Recover foram associados aos riscos que exigem contenção, investigação, reversão ou restauração após um incidente.

## Plano de tratamento dos riscos

| Risco | Controles propostos | Funções relacionadas | Responsáveis | Evidências e verificação |
| ----- | ------------------- | -------------------- | ------------ | ------------------------ |
| R01 — Uso indevido de conta | Autenticação multifator (MFA); bloqueio temporário após múltiplas tentativas de login malsucedidas; notificação ao usuário em caso de login em novo dispositivo; reautenticação antes de operações sensíveis | Govern, Protect, Detect, Respond e Recover | Equipe de desenvolvimento e infraestrutura | Testes de autenticação; análise de logs; simulação de conta comprometida; confirmação do envio de alertas |
| R02 — Alteração indevida de dados do pedido | Validar valores, itens e endereço no backend; recalcular o valor total do pedido no servidor; impedir que dados enviados pelo cliente sejam considerados confiáveis sem validação; registrar alterações relevantes | Protect, Detect, Respond e Recover | Equipe de desenvolvimento | Testes de validação; tentativa controlada de alteração de valores ou itens; análise de registros das operações |
| R03 — Negação de uma operação de entrega | Utilizar confirmação da entrega por código fornecido pelo cliente; registrar mudanças de status do pedido com usuário, data e horário; proteger os registros de auditoria contra alterações indevidas | Govern, Protect, Detect e Respond | Equipe de desenvolvimento e operações | Teste do fluxo de confirmação de entrega; consulta aos logs; teste de rastreabilidade das alterações de status |
| R04 — Acesso indevido a dados de outros usuários | Validar no backend se o recurso solicitado pertence ao usuário autenticado; utilizar identificadores não sequenciais para pedidos e usuários; proteger dados sensíveis em trânsito e em repouso; monitorar consultas anormais | Govern, Protect, Detect, Respond e Recover | Equipe de desenvolvimento e infraestrutura | Testes de autorização; tentativa controlada de acesso a pedidos de terceiros; análise de logs e revisão das permissões |
| R05 — Indisponibilidade do sistema | Aplicar rate limiting por usuário e IP; monitorar picos de tráfego; utilizar escalabilidade da infraestrutura; empregar cache ou outros mecanismos para reduzir carga sobre o backend; utilizar serviços especializados quando necessário | Govern, Identify, Protect, Detect, Respond e Recover | Equipe de infraestrutura e desenvolvimento | Testes de carga; verificação dos limites de requisição; alertas de disponibilidade; simulação de sobrecarga e verificação da recuperação |
| R06 — Obtenção indevida de privilégios administrativos | Utilizar controle de acesso baseado em papéis (RBAC); validar permissões no backend em todas as operações administrativas; separar rotas administrativas; aplicar princípio do menor privilégio e revisar permissões periodicamente | Govern, Protect e Detect | Equipe de desenvolvimento e administradores do sistema | Testes de controle de acesso; tentativa controlada de acesso administrativo com usuário sem permissão; revisão dos papéis e permissões |

## Ordem inicial de implementação

A ordem inicial proposta para implementação dos controles é:

1. **Reforçar os controles de autorização e proteção de dados**, reduzindo os riscos R04 e R06 e também ajudando a prevenir alterações indevidas associadas ao R02.
2. **Proteger as contas e operações sensíveis**, utilizando autenticação reforçada, reautenticação e monitoramento para reduzir o risco R01.
3. **Validar e proteger a integridade dos pedidos**, garantindo que valores, itens e endereços sejam conferidos no backend, reduzindo o risco R02.
4. **Implementar registros de auditoria e mecanismos confiáveis de confirmação de entrega**, reduzindo o risco R03 e auxiliando na investigação de outros incidentes.
5. **Preparar o sistema para horários de pico e tráfego malicioso**, utilizando limitação de requisições, monitoramento e escalabilidade para reduzir o risco R05.
6. **Estabelecer procedimentos de resposta e recuperação**, permitindo conter incidentes, restaurar operações e reduzir seus impactos.
7. **Revisar periodicamente os riscos e controles**, considerando mudanças no sistema, novos tipos de ataque e resultados obtidos nas etapas posteriores do projeto.

A ordem poderá ser alterada conforme as dependências técnicas, os recursos disponíveis e novas informações obtidas sobre os riscos.

## Estimativa do risco residual

A tabela apresenta uma estimativa do nível esperado para cada risco após a implementação e a validação dos controles propostos.

| Risco | Nível inicial | Nível residual esperado | Condição para aceitar o residual |
| ----- | ------------- | ----------------------- | -------------------------------- |
| R01 | Crítico | Médio | MFA, reautenticação, alertas de login e monitoramento funcionando e validados por testes |
| R02 | Alto | Baixo | Validações realizadas no backend e tentativas de manipulação rejeitadas nos testes |
| R03 | Médio | Baixo | Confirmação da entrega e registros de auditoria completos e disponíveis para investigação |
| R04 | Crítico | Médio | Controle de autorização por recurso, proteção dos dados e monitoramento validados por testes |
| R05 | Crítico | Médio | Rate limiting, monitoramento, capacidade de expansão e procedimentos de recuperação testados |
| R06 | Alto | Baixo | Funções administrativas protegidas por autorização no backend e permissões revisadas |

Os níveis residuais representam apenas uma estimativa. A redução dos riscos somente poderá ser confirmada após a implementação dos controles, realização de testes e obtenção de evidências.

Caso os controles não produzam os resultados esperados, os riscos deverão ser reavaliados e novos tratamentos poderão ser definidos.

### Conclusão do tratamento

A análise mostrou que os riscos mais críticos estão relacionados à indisponibilidade do sistema, ao acesso indevido a dados de usuários e ao comprometimento de contas.

A estratégia predominante foi a redução dos riscos por meio de controles de autenticação, autorização, validação no backend, registros de auditoria, monitoramento e proteção da disponibilidade. Em situações específicas, também foram consideradas as estratégias de compartilhamento e aceitação do risco residual.

O NIST CSF permitiu organizar os controles entre as funções Govern, Identify, Protect, Detect, Respond e Recover, demonstrando que a segurança do sistema não depende apenas da prevenção de ataques, mas também da capacidade de identificar, responder e recuperar-se de incidentes.

Os controles propostos ainda deverão ser detalhados nas próximas etapas do projeto. Somente após sua implementação ou descrição detalhada, testes e obtenção de evidências será possível confirmar a redução dos riscos e avaliar se os níveis residuais podem ser aceitos.

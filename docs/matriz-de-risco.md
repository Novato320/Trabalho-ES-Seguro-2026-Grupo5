# Análise e priorização de riscos

A análise de risco foi realizada a partir das ameaças identificadas com o STRIDE e dos casos de abuso descritos anteriormente.

Para cada risco, foram avaliados:

- o evento que pode causar prejuízo;
- a ameaça relacionada;
- a vulnerabilidade ou condição que permite o evento;
- a probabilidade de ocorrência;
- o impacto esperado;
- a pontuação e o nível de prioridade.

## Critérios de probabilidade

| Valor | Classificação | Critério utilizado |
|---|---|---|
| 1 | Baixa | O evento depende de condições incomuns, acesso muito específico ou grande capacidade técnica |
| 2 | Média-baixa | O evento é possível, mas depende de uma vulnerabilidade ou condição específica |
| 3 | Média-alta | O evento é plausível e pode ocorrer em situações comuns de uso ou ataque |
| 4 | Alta | O evento pode ocorrer com facilidade, frequência ou durante condições previsíveis do sistema |

## Critérios de impacto

| Valor | Classificação | Critério utilizado |
|---|---|---|
| 1 | Baixo | Causa pequeno transtorno e pode ser corrigido rapidamente |
| 2 | Moderado | Causa interrupção ou inconsistência limitada, com possibilidade de recuperação |
| 3 | Alto | Causa prejuízo financeiro relevante, exposição de dados ou impacto importante aos usuários |
| 4 | Muito alto | Pode afetar muitos usuários, comprometer pagamentos, entregas ou operações críticas, ou causar prejuízo grave |

## Cálculo e classificação

A pontuação de cada risco é calculada da seguinte forma:

`Pontuação = Probabilidade × Impacto`

| Pontuação | Nível do risco |
|---|---|
| 1 a 3 | Baixo |
| 4 a 7 | Médio |
| 8 a 11 | Alto |
| 12 a 16 | Crítico |

A pontuação auxilia na comparação dos riscos, mas não substitui a justificativa e a análise do contexto.

## Registro de riscos

| ID | Origem STRIDE | Evento de risco | Vulnerabilidade ou condição | Probabilidade | Impacto | Pontuação | Nível |
|---|---|---|---|---:|---:|---:|---|
| R01 | Spoofing | Um atacante acessa a conta de um cliente ou entregador utilizando credenciais comprometidas e realiza operações em seu nome | Credenciais comprometidas ou reutilizadas e ausência de verificação adicional para acessos ou operações sensíveis | 3 | 4 | 12 | Crítico |
| R02 | Tampering | Um usuário altera indevidamente o valor, os itens ou o endereço de entrega de um pedido | Validação insuficiente no backend ou confiança excessiva nos dados enviados pelo cliente | 2 | 4 | 8 | Alto |
| R03 | Repudiation | Um cliente ou entregador nega ter realizado ou recebido uma entrega | Registros de auditoria incompletos, ausência de confirmação confiável da entrega ou falta de informações suficientes para identificar quem realizou a operação | 2 | 3 | 6 | Médio |
| R04 | Information Disclosure | Um usuário acessa dados pessoais, endereços, informações de pedidos ou dados relacionados ao pagamento de outro usuário sem autorização | Falha no controle de autorização e validação inadequada sobre a propriedade do recurso solicitado | 3 | 4 | 12 | Crítico |
| R05 | Denial of Service | O sistema fica indisponível ou apresenta forte degradação durante horários de pico devido a um grande volume de requisições | Ausência de limitação de requisições, monitoramento insuficiente e capacidade inadequada para absorver picos de acesso ou tráfego malicioso | 3 | 4 | 12 | Crítico |
| R06 | Elevation of Privilege | Um estabelecimento, entregador ou cliente obtém acesso indevido a funções administrativas do sistema | Falha no controle de autorização, validação insuficiente de papéis e ausência de verificação de permissões no backend | 2 | 4 | 8 | Alto |

## Justificativas

### R01 — Uso indevido de conta

A probabilidade foi classificada como média-alta porque o comprometimento de credenciais é uma situação plausível, especialmente quando usuários reutilizam senhas ou são vítimas de roubo de credenciais.

O impacto foi classificado como muito alto porque o acesso indevido à conta pode permitir a realização de pedidos fraudulentos, acesso a dados pessoais e endereços e execução de operações em nome da vítima.

### R02 — Alteração indevida de dados do pedido

A probabilidade foi classificada como média-baixa porque o ataque depende de uma falha específica de validação ou autorização no processamento do pedido.

O impacto foi classificado como muito alto porque a alteração indevida de valores, itens ou endereço pode causar prejuízo financeiro, fraude e inconsistências entre cliente, estabelecimento e entregador.

### R03 — Negação de uma operação de entrega

A probabilidade foi classificada como média-baixa porque o problema depende da ausência ou insuficiência de registros confiáveis sobre a entrega e suas confirmações.

O impacto foi classificado como alto porque a falta de evidências pode dificultar a resolução de contestações, causar prejuízos financeiros e gerar conflitos entre cliente, entregador e estabelecimento.

### R04 — Acesso indevido a dados de outros usuários

A probabilidade foi classificada como média-alta porque falhas de autorização podem permitir que um usuário autenticado acesse recursos pertencentes a outros usuários, especialmente quando a aplicação não valida corretamente a propriedade do dado solicitado.

O impacto foi classificado como muito alto devido à possibilidade de exposição de dados pessoais, endereços, informações de pedidos e dados relacionados a pagamentos, podendo causar violação de privacidade, fraude e prejuízo aos usuários.

### R05 — Indisponibilidade do sistema

A probabilidade foi classificada como média-alta porque horários de pico são previsíveis em um aplicativo de delivery e um aumento intenso de requisições pode ocorrer tanto por uso legítimo quanto por ação maliciosa.

O impacto foi classificado como muito alto porque a indisponibilidade pode impedir clientes de realizar pedidos, estabelecimentos de receber solicitações e entregadores de consultar ou atualizar entregas, causando prejuízo financeiro e afetando vários usuários simultaneamente.

### R06 — Obtenção indevida de privilégios administrativos

A probabilidade foi classificada como média-baixa porque o evento depende de uma falha específica no controle de autorização ou na validação das permissões do usuário.

O impacto foi classificado como muito alto porque o acesso a funções administrativas pode permitir alteração de usuários, pedidos, permissões, estabelecimentos e outras informações críticas do sistema.

## Priorização

A ordem inicial de prioridade é:

1. **R05 — Indisponibilidade do sistema:** possui nível crítico e pode afetar simultaneamente clientes, estabelecimentos e entregadores, interrompendo pedidos e entregas e causando prejuízo financeiro em larga escala.

2. **R04 — Acesso indevido a dados de outros usuários:** possui nível crítico e envolve exposição de dados pessoais, endereços, informações de pedidos e dados relacionados a pagamentos, podendo causar violação de privacidade e fraude.

3. **R01 — Uso indevido de conta:** possui nível crítico e pode permitir que um atacante realize pedidos, acesse dados pessoais e execute operações em nome de clientes ou entregadores.

4. **R06 — Obtenção indevida de privilégios administrativos:** possui nível alto e pode permitir alterações amplas em usuários, pedidos, permissões e outras informações críticas do sistema, embora dependa de uma falha específica de autorização.

5. **R02 — Alteração indevida de dados do pedido:** possui nível alto e pode causar fraude, inconsistência nos pedidos e prejuízo financeiro para clientes e estabelecimentos.

6. **R03 — Negação de uma operação de entrega:** possui nível médio e pode dificultar a resolução de contestações e causar prejuízos financeiros, mas tende a afetar uma quantidade mais limitada de usuários por ocorrência.

## Conclusão da análise

A aplicação do STRIDE permitiu identificar diferentes ameaças ao sistema de delivery, enquanto a avaliação de probabilidade e impacto mostrou que esses riscos não possuem a mesma prioridade.

Os riscos críticos deverão receber atenção inicial, especialmente os relacionados à disponibilidade do sistema, à proteção dos dados dos usuários e ao uso indevido de contas.

A classificação representa uma avaliação inicial baseada no contexto definido para o sistema e poderá ser revisada caso surjam novas informações sobre vulnerabilidades, usuários, controles ou incidentes.

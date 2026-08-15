# Etapa 7 — DevSecOps e Vídeo Final

## 1. Planejamento e Modelagem de Ameaças

### 1.1 Modelagem STRIDE
As ameaças foram identificadas por categoria STRIDE, relacionando cada uma a um componente ou ativo crítico do sistema de delivery.

| ID | Categoria | Componente |
|:---|:---|:---|
| T01 | Spoofing | Conta do cliente/entregador |
| T02 | Tampering | Pedido / carrinho de compras |
| T03 | Repudiation | Confirmações de entrega / logs |
| T04 | Information Disclosure | Dados pessoais e de pagamento |
| T05 | Denial of Service | API / backend de pedidos |
| T06 | Elevation of Privilege | Controle de acesso / painel admin |

### 1.2 Análise de Riscos
Os riscos foram priorizados a partir da combinação de Probabilidade (1 a 4) e Impacto (1 a 4), resultando em uma pontuação que define o nível do risco (Crítico, Alto, Médio ou Baixo). Os riscos críticos são os que receberão atenção inicial no pipeline.

#### R01 - Uso indevido de conta (acesso com credenciais roubadas)
- **Probabilidade**: 3 (Média-Alta)
- **Impacto**: 4 (Muito Alto)
- **Pontuação**: 12
- **Nível**: Crítico
#### R04 - Acesso indevido a dados de outros usuários (exposição de privacidade/IDOR)
- **Probabilidade**: 3 (Média-Alta)
- **Impacto**: 4 (Muito Alto)
- **Pontuação**: 12
- **Nível**: Crítico
#### R05 - Indisponibilidade do sistema em horário de pico (ataque ou sobrecarga)
- **Probabilidade**: 3 (Média-Alta)
- **Impacto**: 4 (Muito Alto)
- **Pontuação**: 12
- **Nível**: Crítico

### 1.3 Requisitos de Segurança
A partir da análise de riscos (especialmente os Críticos), foram derivados requisitos de segurança específicos e verificáveis, que devem ser exigidos ao longo de todo o pipeline DevSecOps.
| ID Requisito | Risco | Requisito |
|:---|:---| :--- |
| RS01 | R05 (Indisponibilidade) | O sistema deverá limitar a taxa de requisições por usuário/IP nas operações críticas da API (ex: criação de pedidos, login), rejeitando requisições que excedam o limite. |
| RS02 | R04 (Acesso indevido a dados) | O sistema deverá validar no backend, em toda requisição, se o usuário autenticado é o proprietário do recurso solicitado (pedidos, endereços, pagamentos) antes de retornar ou alterar qualquer informação. |
| RS03 | R01 (Uso indevido de conta) | O sistema deverá exigir uma verificação adicional (reautenticação ou segundo fator - MFA) antes de confirmar operações sensíveis, como cancelamento de pedido, alteração de dados cadastrais ou pagamento. |

### 1.4 Arquitetura de Segurança no Pipeline
A arquitetura de segurança proposta para o sistema de delivery é composta por controles distribuídos em camadas (Edge, Serviços e Dados). No contexto do pipeline DevSecOps (CI/CD), cada um desses controles deve ser exigido, testado e verificado em momentos específicos do fluxo.

| Controle | Pipeline CI/CD | Verificação |
|:---|:---| :--- |
| **Autorização por Recurso** | Implementação / Código | A revisão de código (Pull Request) avalia se todos os endpoints sensíveis chamam essa verificação |
| **Autenticação Multifator (MFA) e Reautenticação** | Implementação e Testes | Testes automatizados validam se operações sensíveis exigem MFA/reautenticação. |
| **Logs Imutáveis e Regras de Detecção** | Monitoramento / Operação | Após o deploy, os logs são centralizados. As Regras de Detecção são ativadas. Alertas críticos disparam um bloqueio automático temporário e notificam a equipe. |

---

## 2. Implementação Segura e Testes 

### 2.1 Práticas de Implementação Segura

Na etapa de implementação, a segurança deve ser incorporada ao desenvolvimento
e não tratada apenas como uma verificação realizada ao final do projeto.

Na Etapa 4 foram documentadas práticas de código seguro relacionadas aos
riscos e requisitos identificados anteriormente. Entre elas, foi utilizada
a reautenticação para operações sensíveis, relacionada ao risco **R01 — Uso
indevido de conta**. Nesse controle, operações de maior impacto, como o
cancelamento de um pedido, somente devem ser executadas após a confirmação de
que o recurso pertence ao usuário e de que uma verificação adicional foi
concluída.

Também foi documentado o controle de autorização por recurso, no qual o
backend verifica se o usuário autenticado possui autorização para acessar o
recurso solicitado antes de executar a operação. Essa prática reduz a
possibilidade de acesso indevido a pedidos, endereços ou outros dados
pertencentes a terceiros.

Em um pipeline DevSecOps, essas práticas devem fazer parte do desenvolvimento
e da revisão do código. Além disso, podem ser incorporadas verificações
automatizadas, como análise estática de código (SAST), análise de dependências
e identificação de segredos ou credenciais adicionados indevidamente ao
repositório.

---

### 2.2 Testes de Segurança

Os controles de segurança devem ser acompanhados por testes que verifiquem
tanto os comportamentos permitidos quanto os comportamentos que devem ser
bloqueados.

Na Etapa 4, os testes de segurança foram definidos antes dos pseudocódigos das
soluções. Na prática de reautenticação, por exemplo, foram considerados um
cenário válido, no qual o usuário autorizado conclui a verificação adicional,
e um cenário inválido, no qual a operação sensível é recusada quando essa
verificação não é concluída.

Da mesma forma, o controle de autorização por recurso considera um cenário em
que o usuário acessa seu próprio recurso e outro em que tenta acessar um
recurso pertencente a outro usuário, situação que deve ser recusada e
registrada.

Em um pipeline DevSecOps, esses testes devem ser executados automaticamente
antes que uma nova versão avance para as próximas etapas. Uma falha em um
teste relacionado a autenticação, autorização ou outra funcionalidade
sensível deve impedir a continuidade do pipeline até que o problema seja
analisado e corrigido.

---

### 2.3 OWASP ZAP

A verificação dinâmica da aplicação foi representada na Etapa 5 por uma sessão
de análise utilizando o **OWASP ZAP** contra uma instância local e autorizada
do **OWASP Juice Shop**, disponibilizada em `http://localhost:3000`.

Foram utilizados mecanismos de descoberta da aplicação e varredura ativa.
Entre os alertas analisados pelo grupo estavam:

- **A01 — Possível SQL Injection:** risco Alto e confiança Baixa, relacionado
  ao CWE-89;
- **A02 — Content Security Policy (CSP) Header Not Set:** risco Médio e
  confiança Alta;
- **A03 — Cabeçalho Anti-Clickjacking Ausente:** risco Médio e confiança
  Média.

No pipeline proposto, uma verificação dinâmica como essa deve ocorrer antes
da implantação. Os resultados não devem ser tratados automaticamente como
vulnerabilidades confirmadas, pois ferramentas automatizadas podem produzir
resultados que necessitam de validação adicional.

Achados relevantes devem ser analisados de acordo com risco, confiança,
impacto e contexto da aplicação. Vulnerabilidades graves confirmadas ou
achados de alto risco ainda não analisados devem impedir que a versão avance
para implantação.

---

### 2.4 Gates / Condições que Impedem o Avanço do Pipeline
Os gates de segurança representam condições que precisam ser satisfeitas
antes que uma versão possa avançar para a próxima etapa do pipeline.

Neste pipeline, a continuidade deverá ser impedida quando ocorrer pelo menos
uma das seguintes situações:

1. **Teste de segurança reprovado:** falhas em testes relacionados a
   autenticação, autorização ou operações sensíveis devem ser corrigidas antes
   do avanço da versão.

2. **Vulnerabilidade crítica ou de alto risco sem análise ou tratamento:**
   um achado relevante identificado por ferramentas como o OWASP ZAP não deve
   ser ignorado. Quando a confiança da ferramenta for baixa, o resultado deve
   ser analisado antes da decisão de liberar ou bloquear a versão.

3. **Segredo ou credencial encontrado no repositório:** senhas, tokens, chaves
   de API ou outras credenciais não devem permanecer versionadas no
   código-fonte.

4. **Dependência conhecida como vulnerável sem avaliação:** uma dependência
   que apresente vulnerabilidade conhecida relevante deve ser atualizada,
   substituída ou ter seu risco avaliado antes da continuidade.

5. **Falha em controle de acesso:** caso os testes demonstrem que um usuário
   consegue acessar recursos ou funções sem a autorização necessária, a
   implantação deve ser bloqueada até a correção.

Essas condições transformam as verificações de segurança em critérios
objetivos para a continuidade do pipeline, evitando que problemas conhecidos
sejam levados diretamente para o ambiente de produção.

---

## 3. Implantação, Monitoramento e Resposta

### 3.1 Implantação Segura

A etapa de implantação é o ponto do pipeline em que o código, já validado pelas etapas anteriores (build, testes, análise estática e verificação com o ZAP), é efetivamente colocado em execução no ambiente de destino. Para que essa etapa seja segura, alguns princípios devem ser seguidos:

- **Implantação condicionada aos gates anteriores.** O deploy só deve ocorrer se todas as verificações de segurança das etapas de Jaques e Ortiz tiverem sido concluídas com sucesso — em especial, os achados de risco Alto do ZAP não podem seguir sem tratamento. O achado **A01 (possível SQL Injection)** da Etapa 5, por exemplo, ainda tem confiança baixa e precisa de validação adicional antes de considerar o gate liberado; os achados **A02 (CSP ausente)** e **A03 (anti-clickjacking ausente)**, por serem confirmados com confiança alta/média, devem ter a correção (cabeçalhos `Content-Security-Policy` e `X-Frame-Options`) efetivamente aplicada e verificada antes do deploy seguir adiante.
- **Verificação automatizada de configuração.** Como parte do próprio pipeline, uma checagem simples (ex.: requisição de teste ao ambiente de staging) deve confirmar que os cabeçalhos de segurança corrigidos por Ortiz (CSP, X-Frame-Options) realmente estão presentes na resposta antes de promover a versão para produção — evitando que uma correção "regrida" silenciosamente em um deploy futuro.
- **Gestão de segredos.** Credenciais, chaves de API e strings de conexão do banco de dados não devem ficar hardcoded no código nem em arquivos de configuração versionados; devem ser injetadas via variáveis de ambiente/gerenciador de segredos do próprio pipeline.
- **Hardening do ambiente de destino.** O ambiente onde a aplicação roda deve seguir princípio de menor privilégio — relevante especialmente para o risco **R06 (obtenção indevida de privilégios administrativos)** e para o achado A01: mesmo que uma injeção de dados ocorra, uma conta de banco de dados com privilégios mínimos limita o estrago possível. Portas e serviços desnecessários devem ficar desabilitados, e a comunicação entre componentes (aplicação, banco de dados) deve ser restrita e, quando possível, criptografada.
- **Rollback.** Deve existir uma estratégia simples de reversão para uma versão anterior estável, caso a nova versão implantada apresente problema de segurança ou funcionamento identificado logo após o deploy — condição que se conecta diretamente à resposta a incidentes (3.3).

### 3.2 Monitoramento

O monitoramento dá continuidade ao que foi estabelecido na Etapa 6, garantindo que os eventos de segurança relevantes continuem sendo coletados e analisados também após a implantação, dentro do próprio ciclo do pipeline DevSecOps. Ele também cobre, em operação, riscos que a verificação estática/ZAP não consegue captar sozinha:

- **Continuidade das regras da Etapa 6.** Os logs de autenticação, autorização e ações administrativas centralizados na Etapa 6 continuam sendo coletados em produção, alimentando as regras R01 (uso indevido de conta) e R06 (privilégios administrativos indevidos).
- **Monitoramento de disponibilidade (risco R05).** Como o risco de indisponibilidade foi classificado como crítico (probabilidade 3, impacto 4), o pipeline deve garantir métricas de volume de requisições, taxa de erros e tempo de resposta por endpoint, permitindo identificar picos anormais de tráfego (uso legítimo em horário de pico vs. possível ataque de negação de serviço) — ligando-se à ameaça STRIDE T05.
- **Monitoramento de erros de aplicação/banco de dados.** Erros do tipo `SQLITE_ERROR` ou similares, como o observado no achado A01 da Etapa 5, não devem apenas gerar um erro HTTP 500 silencioso: devem ser registrados e monitorados em produção, pois uma recorrência desse padrão de erro pode indicar uma tentativa real de exploração da mesma falha identificada na verificação.
- **Verificação periódica de cabeçalhos de segurança.** Complementando o item 3.1, o monitoramento deve incluir uma checagem programada (não só no deploy) de que CSP e X-Frame-Options continuam presentes nas respostas em produção, já que configurações de servidor podem ser alteradas fora do pipeline.
- **Proteção dos próprios registros.** Os logs devem ficar centralizados em local protegido contra alteração indevida e não devem armazenar dados sensíveis (senhas, tokens completos, dados de pagamento), como já definido na Etapa 6.

### 3.3 Resposta a Incidentes

A resposta a incidentes nesta etapa aplica, dentro do contexto de implantação e operação, o fluxo definido na Etapa 6 ("O que acontece depois de um alerta"):

1. **Detecção** — as regras de detecção definidas na Etapa 6 (R01, R06, e demais regras da equipe), somadas ao monitoramento de disponibilidade e de erros descrito em 3.2, continuam operando sobre o ambiente já implantado.
2. **Registro e triagem** — o alerta gerado é registrado com contexto suficiente (regra disparada, conta, IP, rota, endpoint) e classificado como verdadeiro ou falso positivo.
3. **Contenção proporcional ao risco** — para alertas ligados a riscos críticos/altos (R01, R04, R05, R06), a resposta inicial é proporcional: bloqueio de conta ou exigência de nova autenticação para uso indevido de conta (R01); limitação de requisições ou escalonamento de capacidade para indícios de sobrecarga (R05); e, em casos mais graves — como um erro de banco de dados em produção compatível com o padrão do achado A01, ou uma escalada de privilégio administrativa não autorizada (R06) — rollback imediato da implantação recente, caso o incidente esteja associado a uma versão recém-implantada.
4. **Investigação, correção e ajuste** — após a análise, correções são aplicadas (no código, na configuração do ambiente ou nas permissões) e geram um novo ciclo pelo pipeline, passando novamente pelos gates de segurança de Jaques e Ortiz antes de um novo deploy.
5. **Encerramento e aprendizado** — o incidente é documentado e realimenta tanto as regras de detecção da Etapa 6 quanto os controles de implantação desta etapa, fechando o ciclo de melhoria contínua do DevSecOps.

### 3.4 Integração com a Etapa 6

Esta etapa não substitui o trabalho de detecção feito na Etapa 6 — ela o incorpora ao pipeline. A ideia central é que:

- as **regras de detecção definidas na Etapa 6**, relacionadas aos riscos
  selecionados pelo grupo, passam a operar continuamente sobre o ambiente
  de produção alimentado pelo próprio pipeline de deploy, e não como algo à parte;
- o **fluxo de resposta a incidentes** descrito na Etapa 6 (registro → triagem → contenção → investigação → comunicação → correção → encerramento) é o mesmo fluxo referenciado em 3.3, aplicado agora no contexto operacional pós-implantação;
- assim, a Etapa 7 fecha o ciclo DevSecOps: as ameaças e riscos mapeados por Jaques, os controles de código e testes aplicados por Ortiz, e a implantação/monitoramento feitos aqui se conectam à capacidade de detecção e resposta já construída na Etapa 6, formando um pipeline que é seguro desde o planejamento até a operação.

### 3.5 Tabela do Pipeline DevSecOps (linha "Operação")

O enunciado pede uma tabela única com o formato **Momento → Atividade de segurança → Evidência produzida → Condição para continuar**, cobrindo Planejamento, Código, Verificação e Operação. Preenchi apenas a linha de Operação (minha parte); Jaques e Ortiz preenchem as demais e juntamos em uma tabela só na revisão final.

| Momento | Atividade de segurança | Evidência produzida | Condição para continuar |
|---|---|---|---|
| Planejamento | Modelagem de ameaças com STRIDE, análise e priorização de riscos, definição dos requisitos de segurança e arquitetura segura | Modelo STRIDE, matriz de riscos, requisitos de segurança e diagrama de arquitetura | Riscos críticos identificados e tratados por requisitos ou controles de segurança antes do início da implementação |
| Código | Práticas de implementação segura, testes automatizados, SAST, análise de dependências e verificação de segredos | Código/pseudocódigo, resultados dos testes e relatórios das verificações automatizadas | Testes de segurança aprovados; nenhum segredo versionado; nenhuma falha de controle de acesso conhecida |
| Verificação | Análise dinâmica com OWASP ZAP e interpretação dos alertas encontrados | Relatório do ZAP, evidências da execução e análise dos achados A01, A02 e A03 | Nenhuma vulnerabilidade crítica ou de alto risco confirmada sem tratamento e nenhum achado de alto risco pendente de análise |
| **Operação** | Monitoramento de logs de segurança (Etapa 6), métricas de disponibilidade (R05) e recorrência de erros de aplicação/banco (relacionados ao achado A01); aplicação das regras de detecção e resposta a incidentes | Alertas gerados pelas regras de detecção, registros de erro em produção, relatório de incidente (quando houver) | Nenhum alerta crítico em aberto sem triagem; nenhum incidente de risco crítico/alto (R01, R04, R05, R06) sem contenção aplicada |

**Condições que impedem a continuidade do pipeline (a partir da operação):**
- Um alerta de risco crítico/alto (R01, R04, R05 ou R06) gerado em produção fica sem triagem dentro do prazo definido pela equipe.
- Um erro de aplicação/banco de dados compatível com um achado de verificação não corrigido (ex.: padrão semelhante ao A01) volta a ocorrer em produção — isso deve bloquear novos deploys até a causa ser investigada.

---

## 4. Roteiro do Vídeo Final

A apresentação final foi organizada de forma a demonstrar a evolução da
segurança ao longo do ciclo de desenvolvimento do sistema.

### Sequência da apresentação

1. Apresentação do sistema e objetivo do trabalho.
2. Modelagem de ameaças utilizando STRIDE.
3. Análise e priorização dos riscos.
4. Definição dos requisitos e da arquitetura segura.
5. Práticas de implementação segura e testes de segurança.
6. Verificação de vulnerabilidades com OWASP ZAP.
7. Monitoramento e detecção de intrusões.
8. Integração das práticas no pipeline DevSecOps.
9. Considerações finais.

## 5 Vídeo final

A apresentação final do projeto está disponível no YouTube:

[▶️ Assistir à apresentação final](https://youtu.be/UISsBoVC2ME)

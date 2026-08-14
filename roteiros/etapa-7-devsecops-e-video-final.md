# Etapa 7 — DevSecOps e Vídeo Final

> Documento colaborativo. Cada integrante preenche sua seção; ao final, fazemos a revisão conjunta e montamos o roteiro do vídeo.

**Divisão de responsabilidades:**
- **Jaques** — Planejamento / STRIDE, riscos, requisitos e arquitetura no pipeline
- **Ortiz** — Implementação segura, testes, ZAP e condições que impedem o avanço do pipeline
- **Dornelles** — Implantação, monitoramento, resposta e integração com a Etapa 6

---

## 1. Planejamento e Modelagem de Ameaças (Jaques)

### 1.1 Modelagem STRIDE
_(preencher: ameaças identificadas por categoria — Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege)_

### 1.2 Análise de Riscos
_(preencher: riscos priorizados, impacto x probabilidade)_

### 1.3 Requisitos de Segurança
_(preencher: requisitos derivados da análise de ameaças/riscos)_

### 1.4 Arquitetura de Segurança no Pipeline
_(preencher: onde cada controle se encaixa no pipeline CI/CD)_

---

## 2. Implementação Segura e Testes (Ortiz)

### 2.1 Práticas de Implementação Segura
_(preencher: código seguro, SAST, dependências, secrets, etc.)_

### 2.2 Testes de Segurança
_(preencher: testes automatizados incorporados ao pipeline)_

### 2.3 OWASP ZAP
_(preencher: configuração, escopo do scan, resultados)_

### 2.4 Gates / Condições que Impedem o Avanço do Pipeline
_(preencher: critérios de bloqueio — ex.: vulnerabilidades críticas, falha de testes)_

---

## 3. Implantação, Monitoramento e Resposta (Dornelles)

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

- as **regras de detecção** (R01, R02, R03/R06, e demais) definidas na Etapa 6 passam a operar continuamente sobre o ambiente de produção alimentado pelo próprio pipeline de deploy, e não como algo à parte;
- o **fluxo de resposta a incidentes** descrito na Etapa 6 (registro → triagem → contenção → investigação → comunicação → correção → encerramento) é o mesmo fluxo referenciado em 3.3, aplicado agora no contexto operacional pós-implantação;
- assim, a Etapa 7 fecha o ciclo DevSecOps: as ameaças e riscos mapeados por Jaques, os controles de código e testes aplicados por Ortiz, e a implantação/monitoramento feitos aqui se conectam à capacidade de detecção e resposta já construída na Etapa 6, formando um pipeline que é seguro desde o planejamento até a operação.

### 3.5 Tabela do Pipeline DevSecOps (linha "Operação")

O enunciado pede uma tabela única com o formato **Momento → Atividade de segurança → Evidência produzida → Condição para continuar**, cobrindo Planejamento, Código, Verificação e Operação. Preenchi apenas a linha de Operação (minha parte); Jaques e Ortiz preenchem as demais e juntamos em uma tabela só na revisão final.

| Momento | Atividade de segurança | Evidência produzida | Condição para continuar |
|---|---|---|---|
| Planejamento | _(Jaques preenche)_ | _(Jaques preenche)_ | _(Jaques preenche)_ |
| Código | _(Ortiz preenche)_ | _(Ortiz preenche)_ | _(Ortiz preenche)_ |
| Verificação | _(Ortiz preenche)_ | _(Ortiz preenche)_ | _(Ortiz preenche)_ |
| **Operação** | Monitoramento de logs de segurança (Etapa 6), métricas de disponibilidade (R05) e recorrência de erros de aplicação/banco (relacionados ao achado A01); aplicação das regras de detecção e resposta a incidentes | Alertas gerados pelas regras de detecção, registros de erro em produção, relatório de incidente (quando houver) | Nenhum alerta crítico em aberto sem triagem; nenhum incidente de risco crítico/alto (R01, R04, R05, R06) sem contenção aplicada |

**Condições que impedem a continuidade do pipeline (a partir da operação):**
- Um alerta de risco crítico/alto (R01, R04, R05 ou R06) gerado em produção fica sem triagem dentro do prazo definido pela equipe.
- Um erro de aplicação/banco de dados compatível com um achado de verificação não corrigido (ex.: padrão semelhante ao A01) volta a ocorrer em produção — isso deve bloquear novos deploys até a causa ser investigada.

---

## 4. Revisão Final
_(preencher em conjunto após todas as seções acima estarem prontas)_

- [ ] Revisão técnica cruzada entre os três integrantes
- [ ] Consistência entre seções (terminologia, nomes de ferramentas, etc.)
- [ ] Ajustes finais

---

## Notas Gerais
_(espaço livre para observações da equipe)_